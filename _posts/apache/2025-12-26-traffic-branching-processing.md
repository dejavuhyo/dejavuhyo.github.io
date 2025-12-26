---
title: Apache Traffic 분기 처리 방식
author: dejavuhyo
date: 2025-12-26 09:15:00 +0900
categories: [DevOps, Apache]
tags: [apache-traffic, traffic-brancing, 아파치-트래픽, 아파치-트래픽-분기, 트래픽-분기]
---

## 1. Apache HTTP Server (HTTPd) 방식
HTTPd는 주로 `mod_proxy_balancer`와 `mod_rewrite`를 사용하여 트래픽을 정교하게 제어한다.

### 1) 가중치 기반 로드 밸런싱 (Canary 배포용)
서버의 성능이 다르거나, 신규 버전을 10%의 사용자에게만 노출하고 싶을 때 사용한다.

* 동작 원리: 각 `BalancerMember`에 `loadfactor`를 부여하여 전체 합계 중 해당 비중만큼 요청을 할당한다.

* 설정 예시

```text
<Proxy "balancer://app_cluster">
    # 신규 버전(v2)에 10%만 할당 (1 / 1+9)
    BalancerMember "http://v2-server:8080" loadfactor=1
    # 기존 버전(v1)에 90% 할당 (9 / 1+9)
    BalancerMember "http://v1-server:8080" loadfactor=9

    # lbmethod=byrequests: 요청 횟수 기준으로 분배
    ProxySet lbmethod=byrequests
</Proxy>
ProxyPass "/" "balancer://app_cluster/"
```

### 2) 조건부 분기 (RewriteRule 활용)
특정 조건(Cookie, IP, Header)에 맞는 요청만 다른 백엔드로 보낼 때 매우 유용하다.

* 예시 (특정 그룹 A/B 테스트): 쿠키 값에 `beta=true`가 포함된 사용자만 테스트 서버로 보낸다.

```text
RewriteEngine On
# 쿠키 검사
RewriteCond %{HTTP_COOKIE} beta=true [NC]
# 일치하면 테스트 서버로 프록시
RewriteRule "^/(.*)" "http://test-server:8080/$1" [P,L]

# 나머지 일반 트래픽
ProxyPass "/" "http://prod-server:8080/"
```

## 2. Apache Traffic Server (ATS) 방식
ATS는 고성능 캐시 엔진이기 때문에 `remap.config`와 `strategies.yaml`을 통해 대규모 트래픽을 처리한다.

### 1) Next Hop Selection (strategies.yaml)
여러 개의 오리진(Origin) 서버가 있을 때 어떤 방식으로 선택할지 정의한다.

* Consistent Hashing (일관된 해싱): 특정 URL이나 클라이언트 IP를 항상 같은 서버로 보낼 때 사용한다. 세션 유지가 중요한 서비스에 필수적이다.

* 설정 개념

```yaml
# strategies.yaml (ATS 9.x 이상)
strategies:
  - strategy: 'my-strategy'
    protocol: http
    policy: consistent_hash   # 해싱 방식 선택
    groups:
      - &g1
        nodes:
          - host: 'host1.example.com'
            weight: 1.0
          - host: 'host2.example.com'
            weight: 1.0
```

### 2) Header Rewrite 플러그인
요청이 들어오는 시점에 헤더를 조작하여 다른 경로로 넘긴다.

* 사용 사례: 모바일 기기(User-Agent) 접속자만 모바일 전용 오리진 서버로 분기 처리한다.

## 3. 기술 선택 기준

|  | 추천 기술 | 설명 |
|:-----:|:-----:|:-----:|
| 단순한 웹 서버 앞단 | Apache HTTPd | 설정이 직관적이고 `mod_rewrite`로 유연한 처리가 가능함 |
| 고가용성/대용량 트래픽 | Apache Traffic Server | 캐싱 성능이 뛰어나며 대규모 서버 팜 관리에 최적화됨 |
| 세션 유지가 중요할 때 | Sticky Session (Cookie) | `mod_proxy_balancer`에서 `stickysession` 옵션 사용 가능 |
| 특정 지역/IP별 분기 | GeoIP 기반 분기 | `mod_geoip` 모듈을 연동하여 국가별로 다른 서버 연결 가능 |

## 4. 주의사항

* Health Check: 분기 처리 시 특정 백엔드가 죽으면 트래픽을 차단해야 한다. `mod_proxy_hcheck` 모듈을 반드시 함께 사용해야 한다.

* 세션 불일치: 가중치 기반으로 나누더라도 사용자가 페이지를 이동할 때마다 서버가 바뀌면 로그인이 풀릴 수 있다. 이 경우 Sticky Session 설정을 꼭 확인해야 한다.
