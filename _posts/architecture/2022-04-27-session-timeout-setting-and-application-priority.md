---
title: 세션 Timeout 설정 및 적용 우선순위
author: dejavuhyo
date: 2022-04-27 10:10:00 +0900
categories: [Application, Architecture]
tags: [session-timeout, session-setting, session-priority, 세션-타임아웃, 세션-설정, 세션-우선순위]
---

## 1. 세션 Timeout
Servlet에서 모든 세션 객체는 Timeout 값을 가지고 있으며 따로 설정하지 않는 경우 기본적으로 30분이다.

## 2. 우선순위

### 1) 첫 번째
setMaxInactiveInterval(int interval) 메서드로 지정한 경우이다.

특정 세션 객체에 직접 `setMaxInactiveInterval(int interval)` 메서드를 통해 timeout을 지정한 경우 해당 값이 가장 우선순위로 적용된다. 이 경우 timeout 값의 단위는 초(second) 단위이다.

```java
HttpSession session = request.getSession();
session.setMaxInactiveInterval(1800);
```

### 2) 두 번째
`WEB-INF/web.xml`에 설정된 timeout 값이다.

웹 애플리케이션의 배포서술자인 `web.xml`을 통해 timeout 지정이 가능하며, 이곳에 설정된 timeout 값이 두 번째 우선순위를 갖는다.

setMaxInactiveInterval() 메서드를 사용하지 않았다면 이 설정을 통해 지정된 timeout이 설정된다. 이곳의 timeout 설정 단위는 분(minute) 단위이다.

```xml
<session-config>
    <session-timeout>30</session-timeout>
</session-config>
```

### 3) 세 번째
`[톰캣 설치 디렉터리]/conf/web.xml`에 설정된 timeout 값이다.

톰캣의 conf 디렉터리에는 웹 애플리케이션과 마찬가지로 `web.xml` 파일이 있다. 웹 애플리케이션에서 아무런 설정을 하지 않는 경우 이곳에 설정된 기본값인 30분이 적용된다.

```xml
<session-config>
    <session-timeout>30</session-timeout>
</session-config>
```

> timeout 값을 `-1`로 지정한 경우 세션은 강제로 제거해주지 않으면 무한으로 유지된다. 그러나 메모리관리나 보안상 좋지 않으므로 자주 사용하는 설정은 아니다.

## [출처 및 참고]
* [https://dololak.tistory.com/706](https://dololak.tistory.com/706)
* [https://prinha.tistory.com/entry/JAVA-SessionTimeOut-%EC%84%A4%EC%A0%95-%EC%84%B8%EC%85%98-%EC%8B%9C%EA%B0%84-%EC%A0%9C%EC%96%B4](https://prinha.tistory.com/entry/JAVA-SessionTimeOut-%EC%84%A4%EC%A0%95-%EC%84%B8%EC%85%98-%EC%8B%9C%EA%B0%84-%EC%A0%9C%EC%96%B4)
