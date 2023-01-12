---
title: Java Client IP 가져오기
author: dejavuhyo
date: 2021-08-24 06:00:00 +0900
categories: [Language, Java]
tags: [java-client-ip, client-ip, user-ip, 클라이언트-ip, 사용자-ip, 접속자-ip]
---

## 1. X-Forwarded-For
X-Forwarded-For (XFF) 헤더는 HTTP 프록시나 로드 밸런서를 통해 웹 서버에 접속하는 클라이언트의 원 IP 주소를 식별하는 사실상의 표준 헤더다. 클라이언트와 서버 중간에서 트래픽이 프록시나 로드 밸런서를 거치면, 서버 접근 로그에는 프록시나 로드 밸런서의 IP 주소만을 담고 있다. 클라이언트의 원 IP 주소를 보기위해 X-Forwarded-For 요청 헤더가 사용된다.

이 헤더는 디버깅, 통계, 그리고 위치 종속적인 컨텐츠를 위해 사용되고, 클라이언트의 IP 주소 등과 같은 민감한 개인정보를 노출시킨다. 그러므로 이 헤더를 사용할 때에는 사용자의 프라이버시를 주의해야 한다.

이 헤더의 표준화된 버전은 HTTP Forwarded 헤더다.

X-Forwarded-For 은 이메일 메시지가 다른 계정으로부터 포워딩되었음을 나타내는 이메일 헤더이기도 하다.

## 2. 문법

```text
X-Forwarded-For: <client>, <proxy1>, <proxy2>
```

* **client:** 클라이언트 IP 주소

* **proxy1, proxy2:** 하나의 요청이 여러 프록시들을 거치면, 각 프록시의 IP 주소들이 차례로 열거된다. 즉, 가장 오른쪽 IP 주소는 가장 마지막에 거친 프록시의 IP 주소이고, 가장 왼쪽의 IP 주소는 최초 클라이언트의 IP 주소이다.

## 3. Client IP 가져오기

```java
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.http.HttpServletRequest;

public class ClientIP {

    public String getClientIP() throws Exception {

        String ip = null;
        HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.currentRequestAttributes()).getRequest();
        ip = request.getHeader("X-Forwarded-For");

        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("Proxy-Client-IP");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("WL-Proxy-Client-IP");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("HTTP_CLIENT_IP");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("HTTP_X_FORWARDED_FOR");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("X-Real-IP");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("X-RealIP");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getHeader("REMOTE_ADDR");
        }
        if (ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {
            ip = request.getRemoteAddr();
        }

        return ip;
    }
}
```

## [출처 및 참고]
* [https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Forwarded-For](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Forwarded-For)
* [https://linked2ev.github.io/java/2019/05/22/JAVA-1.-java-get-clientIP/](https://linked2ev.github.io/java/2019/05/22/JAVA-1.-java-get-clientIP/)
* [https://admm.tistory.com/80](https://admm.tistory.com/80)
