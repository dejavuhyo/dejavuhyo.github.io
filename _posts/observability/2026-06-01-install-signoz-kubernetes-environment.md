---
title: Kubernetes 환경에 SigNoz 설치 방법
author: dejavuhyo
date: 2026-06-01 11:00:00 +0900
categories: [DevOps, Observability]
tags: [signoz, signoz-install, kubernetes, signoz-otel-collector, signoz-clickhouse, signoz-otel-collector, signoz-zookeeper]
---

![signoz-logo](/assets/img/2026-06-01-install-signoz-kubernetes-environment/signoz-logo.png)

## 1. SigNoz
쿠버네티스(Kubernetes) 환경에서 오픈소스 observability(모니터링) 툴인 SigNoz를 설치하는 방법은 Helm을 사용하는 것이다.

SigNoz는 내부적으로 로그, 트레이스, 메트릭 저장을 위해 ClickHouse를 사용하며, 데이터 수집을 위해 OpenTelemetry Collector를 활용한다.

Helm 차트를 이용하면 컴포넌트들을 한 번에 쉽게 배포할 수 있다.

## 2. 사전 준비 사항

* Kubernetes 클러스터: v1.22 이상의 버전을 권장한다.

* Helm: v3 이상이 설치되어 있어야 한다.

* 스토리지 클래스(StorageClass): ClickHouse와 대시보드 데이터 저장을 위한 동적 볼륨 프로비저닝(Persistent Volume)이 지원되어야 한다.

## 3. SigNoz 설치

### 1) Helm 리포지토리 추가 및 업데이트
SigNoz 공식 Helm 리포지토리를 추가하고 최신 상태로 업데이트한다.

```bash
# SigNoz Helm 리포지토리 추가
helm repo add signoz https://charts.signoz.io

# 리포지토리 업데이트
helm repo update
```

### 2) 네임스페이스(Namespace) 생성
SigNoz 컴포넌트들을 격리하여 관리할 네임스페이스를 생성한다.

보통 `platform` 또는 `signoz`라는 이름으로 많이 생성한다. (platform으로 진행)

```bash
kubectl create namespace platform
```

### 3) 설정 파일(`values.yaml`) 준비
운영(Production) 환경이나 특정 클러스터 환경(EKS, GKE, 자체 구축 등)에 맞게 설정을 커스텀하고 싶다면 기본 `values.yaml`을 다운로드하여 수정하는 것이 좋다.

```bash
# 기본 values.yaml 파일 다운로드
helm show values signoz/signoz > values.yaml
```

* StorageClass 설정: 만약 클러스터의 기본(Default) 스토리지 클래스가 없다면, `values.yaml` 내에서 `clickhouse.persistence.storageClass` 설정을 찾아서 사용 중인 스토리지 클래스 이름(예: `gp2`, `standard` 등)을 명시해 주어야 한다.

* 리소스 제한: 테스트 환경이라면 ClickHouse나 내부 Kafka의 CPU/Memory Request와 Limit을 낮춰주는 것이 좋다.

### 4) Helm 차트로 SigNoz 설치
설정을 마쳤다면 SigNoz를 설치한다.

* `values.yaml`을 적용하여 설치

```bash
helm install signoz signoz/signoz -n platform -f values.yaml
```

* 기본 설정으로 빠른 설치

```bash
helm install signoz signoz/signoz -n platform
```

### 5) 설치 상태 확인
모든 Pod가 정상적으로 뜨기까지 약 2~5분 정도 소요될 수 있다. ClickHouse와 Kafka가 먼저 준비된 후 SigNoz 웹 UI가 활성화된다.

```bash
kubectl get pods -n platform -w
```

> 모든 Pod의 `STATUS`가 `Running`이 될 때까지 기다린다.

## 4. Pending 상태 오류

### 1) 오류

```text
NAME                                          READY   STATUS     RESTARTS       AGE
chi-signoz-clickhouse-cluster-0-0-0           0/1     Pending    0              11m
signoz-0                                      0/1     Pending    0              12m
signoz-clickhouse-operator-7ff66dbbfb-rcvbj   2/2     Running    0              12m
signoz-otel-collector-5ccd84c487-vb22h        0/1     Init:0/1   1 (119s ago)   12m
signoz-telemetrystore-migrator-x8mrz          0/1     Init:0/3   0              5m52s
signoz-zookeeper-0                            0/1     Pending    0              12m 
```

10분 넘게 핵심 컴포넌트들이 `Pending` 상태에 머물러 있는 것은 MicroK8s에 스토리지(StorageClass) 기능이 켜져 있지 않아서 발생한 문제이다.

`clickhouse-cluster`, `signoz-0` (대시보드 메타데이터 저장용), `zookeeper-0`는 모두 데이터를 영구적으로 저장해야 하는 파드(StatefulSet)들이다.

데이터를 저장할 디스크(볼륨)를 요청했는데, 쿠버네티스가 디스크를 빌려주지 못해 계속 대기(Pending)하고 있는 상태이다.

따라서 데이터를 수집하는 `otel-collector`도 초기화(Init) 단계에서 멈춰있는 것이다.

### 2) 해결 방법

#### (1) MicroK8s 내장 스토리지 애드온 활성화
MicroK8s는 호스트 서버의 디스크 공간을 쿠버네티스 볼륨으로 쓸 수 있는 기본 스토리지 기능(`hostpath-storage`)을 제공한다.

```bash
microk8s enable storage
```

> `Storage클래스가 성공적으로 생성되었다`는 메시지가 뜬다.

#### (2) 스토리지 클래스 생성 확인
`microk8s-hostpath`라는 이름의 스토리지 클래스가 생겼고, `(default)`가 붙어있는지 확인한다.

```bash
kubectl get sc
```

```text
NAME                          PROVISIONER            RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
microk8s-hostpath (default)   microk8s.io/hostpath   Delete          WaitForFirstConsumer   false                  12s
```

스토리지 애드온이 켜지고 기본(Default) 스토리지 클래스가 등록되면, 쿠버네티스가 `Pending` 상태로 대기 중이던 파드들을 감지하고 자동으로 디스크 볼륨을 생성해 연결해 준다.

별도로 SigNoz를 지우고 다시 설치할 필요는 없다. 설치 상태를 확인한다.

## 5. SigNoz 웹 UI 접속 방법
설치가 완료되면 웹 브라우저를 통해 SigNoz 대시보드에 접속해야 한다. 접속 방법은 크게 3가지가 있다.

### 1) (방법 1) Port-Forwarding (로컬 테스트용)
가장 빠르고 간단하게 확인해 볼 수 있는 방법이다. 로컬 PC의 3301 포트를 SigNoz Frontend 서비스로 포워딩한다.

```bash
kubectl port-forward svc/signoz 8080:8080 -n platform --address 0.0.0.0
```

`http://<MicroK8s-서버-IP>:8080`로 접속하면 계정 생성 화면이 나타난다.

> 주의: 이 명령어가 실행된 터미널 창은 브라우저를 사용하는 동안 그대로 켜두셔야 한다. 터미널 창을 닫으면 연결이 끊어진다.

### 2) (방법 2) LoadBalancer 사용 (클라우드 환경)
AWS EKS나 GCP GKE 같은 환경을 사용 중이라면 서비스를 LoadBalancer 타입으로 변경하여 외부 IP를 직접 할당받을 수 있다. (`values.yaml`에서 `frontend.service.type`을 `LoadBalancer`로 변경해도 된다.)

```bash
kubectl patch svc signoz -n platform -p '{"spec": {"type": "LoadBalancer", "ports": [{"port": 8080, "targetPort": 8080}]}}'
```

잠시 후 아래 명령어로 생성된 외부 IP(EXTERNAL-IP)를 확인하여 접속한다.

```bash
kubectl get svc signoz -n platform
```

### 3) (방법 3) Ingress 설정 (운영 환경)
도메인을 연결하고 SSL(HTTPS)을 적용하려면 인그레스(Ingress)를 구성하는 것이 좋다. 아래는 예시 템플릿이다.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: signoz-ingress
  namespace: platform
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
  - host: signoz.yourdomain.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: signoz
            port:
              number: 8080
```

## 6. SigNoz UI 접속 오류

### 1) LoadBalancer 사용 접속 오류

#### (1) `signoz` 이름으로 패치

```bash
kubectl patch svc signoz -n platform -p '{"spec": {"type": "LoadBalancer"}}'
```

#### (2) 패치 성공 후 외부 IP 확인

```bash
kubectl get svc -n platform
```

#### (3) MetalLB 애드온 활성화 (IP 지정)
MicroK8s의 내장 로드밸런서에 서버 IP를 할당한다. 시작 IP와 끝 IP를 지정하면 된다.

`192.168.98.137`를 사용하였다.

```bash
microk8s enable metallb:192.168.98.137-192.168.98.137
```

> 성공적으로 활성화되었다는 메시지가 뜰 때까지 기다린다.

#### (4) 서비스 설정 재확인
이미 `LoadBalancer` 타입으로 바꿨지만, 포트가 정확히 외부 8080으로 고정되도록 실행한다.

```bash
kubectl patch svc signoz -n platform -p '{"spec": {"type": "LoadBalancer", "ports": [{"name": "http", "port": 8080, "targetPort": 8080}]}}'
```

#### (5) `<pending>` 해제 여부 확인

```bash
kubectl get svc signoz -n platform
```

* 정상적인 경우

```text
NAME     TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                                        AGE
signoz   LoadBalancer   10.152.183.175   192.168.98.137   8080:32397/TCP,8085:30962/TCP,4320:32626/TCP   109m
```

> `EXTERNAL-IP` 위치에 `<pending>` 대신 `192.168.98.137`이 정상적으로 표시되면 성공이다.

* 접속 확인

```text
http://192.168.98.137:8080
```

### 2) 포트 에러

```text
error: Service signoz does not have a service port 3301
```

SigNoz 최신 버전 (v0.125.0 내외)에서는 웹 UI와 내부 API가 signoz라는 단일 서비스로 묶이면서 포트 번호가 `8080`으로 변경되었다.

```bash
kubectl port-forward svc/signoz 8080:8080 -n platform --address 0.0.0.0
```

* 접속 확인

```text
http://<MicroK8s-서버-IP>:8080
```

## [출처 및 참고]
* [https://signoz.io/docs/install/kubernetes](https://signoz.io/docs/install/kubernetes)
* [https://github.com/SigNoz/charts](https://github.com/SigNoz/charts)
* [https://github.com/SigNoz](https://github.com/SigNoz)
