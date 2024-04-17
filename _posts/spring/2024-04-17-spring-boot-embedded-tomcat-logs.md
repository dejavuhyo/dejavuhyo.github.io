---
title: Spring Boot 내장 Tomcat 로그
author: dejavuhyo
date: 2024-04-17 11:08:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-tomcat, embedded-tomcat, tomcat-logs, spring-tomcat-logs]
---

## 1. Sample Application
REST API를 만들고, 사용자에게 인사하는 GreetingsController를 정의한다.

```java
@GetMapping("/greetings/{username}")
public String getGreetings(@PathVariable("username") String userName) {
    return "Hello " + userName + ", Good day...!!!";
}
```

## 2. Tomcat 로그 유형
내장된 Tomcat은 두 가지 유형의 로그를 저장한다.

* Access logs

* Internal server logs

access logs는 애플리케이션에서 처리된 모든 요청의 기록을 유지한다. 이러한 로그는 페이지 조회수, 사용자 세션 활동 등을 추적 하는데 사용될 수 있다. 반면, internal server logs는 실행 중인 애플리케이션의 문제를 해결하는데 도움이 된다.

## 3. 접속 로그
기본적으로 액세스 로그는 활성화되어 있지 않다.

하지만 `application.properties`에 속성을 추가하면 쉽게 활성화할 수 있다.

```properties
server.tomcat.accesslog.enabled=true
```

마찬가지로 VM 인수를 사용하여 액세스 로그를 활성화할 수 있다.

```shell
java -jar -Dserver.tomcat.basedir=tomcat -Dserver.tomcat.accesslog.enabled=true app.jar
```

이러한 로그 파일은 임시 디렉터리에 생성된다. 예를 들어 Windows에서 액세스 로그 디렉터리는 `AppData\Local\Temp\tomcat.2142886552084850151.40123\logs`와 같다.

### 1) Format
이 속성을 활성화하면 실행 중인 애플리케이션에 다음과 같은 내용이 표시된다.

```text
0:0:0:0:0:0:0:1 - - [13/May/2019:23:14:51 +0530] "GET /greetings/Harry HTTP/1.1" 200 27
0:0:0:0:0:0:0:1 - - [13/May/2019:23:17:23 +0530] "GET /greetings/Harry HTTP/1.1" 200 27
```

이는 액세스 로그이며 형식은 다음과 같습다.

```text
%h %l %u %t \"%r\" %>s %b
```

다음과 같이 해석할 수 있다.

* `%h` - 요청을 보낸 클라이언트 IP, 이 경우 `0:0:0:0:0:0:0:1`
* `%l` - 사용자의 ID
* `%u` - HTTP 인증에 의해 결정된 사용자 이름
* `%t` - 요청이 수신된 시간
* `%r` - 클라이언트의 요청 라인, 이 경우 GET `/greetings/Harry HTTP/1.1`
* `%>s` - 서버에서 클라이언트로 전송된 상태 코드,  여기서는 200
* `%b` - 클라이언트에 대한 응답 크기 또는 이러한 요청의 경우 27

이 요청에는 인증된 사용자가 없으므로 `%l` 및 `%u`는 대시를 인쇄했다.

실제로 정보가 누락된 경우 Tomcat은 해당 슬롯에 대해 대시를 인쇄한다.

### 2) 액세스 로그 사용자 정의
`application.properties`에 몇 가지 속성을 추가하여 기본 Spring Boot 구성을 재정의할 수 있다.

먼저 기본 로그 파일 이름을 변경하려면 다음을 수행한다.

```properties
server.tomcat.accesslog.suffix=.log
server.tomcat.accesslog.prefix=access_log
server.tomcat.accesslog.file-date-format=.yyyy-MM-dd
```

또한 로그 파일의 위치를 ​​변경할 수 있다.

```properties
server.tomcat.basedir=tomcat
server.tomcat.accesslog.directory=logs
```

마지막으로 로그 파일에 로그가 기록되는 방식을 재정의할 수 있다.

```properties
server.tomcat.accesslog.pattern=common
```

Spring Boot에는 [구성 가능한 속성](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html)이 몇 가지 더 있다.

## 4. 내부 로그
Tomcat 서버의 내부 로그는 서버 측 문제를 해결하는데 매우 유용하다.

이러한 로그를 보려면 `application.properties`에 아래 로깅 구성을 추가해야 한다.

```properties
logging.level.org.apache.tomcat=DEBUG
logging.level.org.apache.catalina=DEBUG
```

그러면 다음과 같은 내용이 표시된다.

```text
2019-05-17 15:41:07.261 DEBUG 31160 --- [0124-Acceptor-0] o.apache.tomcat.util.threads.LimitLatch  : Counting up[http-nio-40124-Acceptor-0] latch=1
2019-05-17 15:41:07.262 DEBUG 31160 --- [0124-Acceptor-0] o.apache.tomcat.util.threads.LimitLatch  : Counting up[http-nio-40124-Acceptor-0] latch=2
2019-05-17 15:41:07.278 DEBUG 31160 --- [io-40124-exec-1] org.apache.tomcat.util.modeler.Registry  : Managed= Tomcat:type=RequestProcessor,worker="http-nio-40124",name=HttpRequest1
...
2019-05-17 15:41:07.279 DEBUG 31160 --- [io-40124-exec-1] m.m.MbeansDescriptorsIntrospectionSource : Introspected attribute virtualHost public java.lang.String org.apache.coyote.RequestInfo.getVirtualHost() null
...
2019-05-17 15:41:07.280 DEBUG 31160 --- [io-40124-exec-1] o.a.tomcat.util.modeler.BaseModelMBean   : preRegister org.apache.coyote.RequestInfo@1e6f89ad Tomcat:type=RequestProcessor,worker="http-nio-40124",name=HttpRequest1
2019-05-17 15:41:07.292 DEBUG 31160 --- [io-40124-exec-1] org.apache.tomcat.util.http.Parameters   : Set query string encoding to UTF-8
2019-05-17 15:41:07.294 DEBUG 31160 --- [io-40124-exec-1] o.a.t.util.http.Rfc6265CookieProcessor   : Cookies: Parsing b[]: jenkins-timestamper-offset=-19800000
2019-05-17 15:41:07.296 DEBUG 31160 --- [io-40124-exec-1] o.a.c.authenticator.AuthenticatorBase    : Security checking request GET /greetings/Harry
2019-05-17 15:41:07.296 DEBUG 31160 --- [io-40124-exec-1] org.apache.catalina.realm.RealmBase      :   No applicable constraints defined
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-embedded-tomcat-logs](https://www.baeldung.com/spring-boot-embedded-tomcat-logs)
