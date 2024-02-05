---
title: Spring Boot Embedded Tomcat Configuration
author: dejavuhyo
date: 2024-02-05 16:21:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-tomcat, tomcat-configuration, embedded-tomcat, tomcat-configuration, 스프링-부트-톰켓, 톰켓-구성]
---

## 1. Embedded Tomcat
기본 Spring Boot 웹 응용 프로그램은 내장된 Tomcat 컨테이너를 일시적 종속성으로 포함한다. 즉, `spring-boot-starter-web`은 `spring-boot-starter-tomcat` 종속성을 일시적으로 가져온다.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

```text
[INFO] com.howtodoinjava:rest-api-crud-example:jar:1.0-SNAPSHOT
[INFO] +- org.springframework.boot:spring-boot-starter-web:jar:3.1.2:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter:jar:3.1.2:compile
...
...
[INFO] |  +- org.springframework.boot:spring-boot-starter-tomcat:jar:3.1.2:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-core:jar:10.1.11:compile
[INFO] |  |  \- org.apache.tomcat.embed:tomcat-embed-websocket:jar:10.1.11:compile
...
...
```

## 2. Properties를 사용하여 내장 Tomcat Configuration
기본 Tomcat 서버에는 [Tomcat 관련 속성](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#application-properties.server.server.tomcat.accept-count)으로 사용자 정의할 수 있는 사전 구성된 기본 동작이 함께 제공된다.


### 1) 서버 포트 및 LocalHost 주소

* application.properties

```properties
server.address=my_custom_ip
server.port=80
```

* `server.port` - 서버 HTTP 포트. 8080이 기본값이다.

* `server.address` - 서버가 바인딩되어야 하는 네트워크 주소이다. 기본값은 `0.0.0.0` 모든 IPv4 주소를 통한 연결을 허용하는 것이다.

### 2) 원격 연결 속성

* application.properties

```properties
server.connection-timeout=10s
server.max-http-header-size=8KB

server.tomcat.accept-count=100
server.tomcat.max-connections=10000
server.tomcat.max-threads=200
server.tomcat.min-spare-threads=10
server.tomcat.max-swallow-size=2MB
server.tomcat.max-http-post-size=2MB
```

* `server.connection-timeout` - 커넥터가 연결을 닫기 전에 다른 HTTP 요청을 기다리는 시간이다. 설정하지 않으면 커넥터의 컨테이너별 기본값이 사용된다. 무한 시간 초과를 나타내려면 `-1` 값을 사용한다.

* `server.max-http-header-size` - HTTP 메시지 헤더의 최대 크기이다.

* `server.tomcat.accept-count` - 가능한 모든 요청 처리 스레드가 사용 중일 때 들어오는 연결 요청의 최대 대기열 길이이다.

* `server.tomcat.max-connections` - 서버가 주어진 시간에 수락하고 처리하는 최대 연결 수이다.

* `server.tomcat.max-threads` - 최고 부하가 있는 서버의 최대 작업자 스레드 수이다. 즉, 처리할 수 있는 최대 동시 요청 수이다.

* `server.tomcat.min-spare-threads` - 항상 실행되는 최소 스레드 수이다. 여기에는 활성 스레드와 유휴 스레드가 모두 포함된다.

* `server.tomcat.max-swallow-size` - 중단된 업로드를 위해 Tomcat에서 삼킬 요청 본문 바이트의 최대 수(전송 인코딩 오버헤드 제외)이다. 중단된 업로드는 Tomcat이 요청 본문이 무시될 것임을 알고 있지만 클라이언트가 여전히 요청 본문을 보내는 경우이다. Tomcat이 본문을 삼키지 않으면 클라이언트는 응답을 볼 가능성이 없다. 지정하지 않으면 기본값 `2097152(2MB)`가 사용된다. 0보다 작은 값은 제한이 적용되지 않음을 나타낸다.

* `server.tomcat.max-http-post-size` - HTTP 게시물 콘텐츠의 최대 크기이다.

### 3) 오류 처리

* application.properties

```properties
server.error.include-exception=false
server.error.include-stacktrace=never
server.error.path=/error
server.error.whitelabel.enabled=true
```

* `server.error.include-exception` - 오류 보기를 렌더링하는 동안 "exception" 속성을 포함한다. 기본값은 false이다.

* `server.error.include-stacktrace` - 오류 보기를 렌더링하는 동안 "stacktrace" 속성을 포함한다. 기본값은 never이다.

* `server.error.path` - 기본 화이트라벨 오류 페이지 경로 URL이다.

* `server.error.whitelabel.enabled` - 서버 오류가 발생한 경우 브라우저에 표시되는 기본 오류 페이지의 활성화 여부이다.

### 4) HTTPS 또는 SSL 구성

* application.properties

```properties
server.ssl.enabled=true
server.ssl.protocol=TLS1.2

server.ssl.key-alias=tomcat
server.ssl.key-store=keystore-path
server.ssl.key-store-type=keystore-type
server.ssl.key-store-provider=provider
server.ssl.key-store-password=some-password

server.ssl.trust-store=store-path
server.ssl.trust-store-type=JKS
server.ssl.trust-store-provider=provider
server.ssl.trust-store-password=some-password
```

* `server.ssl.enabled` - 커넥터에서 SSL 트래픽을 활성화하려면 이 속성을 사용한다. 커넥터에서 SSL handshake/encryption/decryption을 활성화하려면 이 값을 true로 설정한다.

* `server.ssl.protocol` - 사용할 SSL 프로토콜이다. 기본값은 TLS이다. 이 [목록](https://docs.oracle.com/javase/7/docs/technotes/guides/security/StandardNames.html#SSLContext)에서 값을 선택한다.

* `server.ssl.key-alias` - 키 저장소의 서버 키 및 인증서에 사용되는 별칭이다. 지정하지 않으면 키 저장소에서 읽은 첫 번째 키가 사용된다.

* `server.ssl.key-store` - 로드할 서버 인증서를 저장한 키 저장소 파일의 경로 이름이다.

* `server.ssl.key-store-type` - 서버 인증서에 사용할 키 저장소 파일 유형이다. 지정하지 않을 경우 기본값은 "JKS"이다.

* `server.ssl.key-store-provider` - 서버 인증서에 사용될 키 저장소 공급자의 이름이다. 지정하지 않으면 등록된 공급자 목록을 우선 순위에 따라 순회하고 keystoreType을 지원하는 첫 번째 공급자가 사용된다.

* `server.ssl.key-store-password` - 지정된 키 저장소 파일에 액세스하는데 사용되는 비밀번호이다. 기본값은 keyPass 속성의 값이다.

* `server.ssl.trust-store` - 클라이언트 인증서의 유효성을 검사하는데 사용할 신뢰 저장소 파일이다.

* `server.ssl.trust-store-type` - 신뢰 저장소에 사용되는 키 저장소 유형이다. 해당 속성을 지정하지 않으면 키 저장소 유형 값이 기본값으로 사용된다.

* `server.ssl.trust-store-provider` - 서버 인증서에 사용될 신뢰 저장소 제공자의 이름이다. 해당 속성이 null이면 키 저장소 공급자의 값이 기본값으로 사용된다.

* `server.ssl.trust-store-password` - 신뢰 저장소에 액세스하기 위한 비밀번호이다. 해당 속성이 비어 있으면 신뢰 저장소 비밀번호가 구성되지 않는다.

### 5) HTTP 액세스 로깅

* application.properties

```properties
server.tomcat.accesslog.enabled=true
server.tomcat.accesslog.directory=logs
server.tomcat.accesslog.file-date-format=yyyy-MM-dd
server.tomcat.accesslog.prefix=access_log
server.tomcat.accesslog.suffix=.log
server.tomcat.accesslog.rotate=true
```

* `server.tomcat.accesslog.enabled` - 액세스 로깅을 활성화할지 여부이다.

* `server.tomcat.accesslog.directory` - 로그 파일이 생성되는 디렉터리이다. Tomcat 기본 디렉토리에 대해 절대적이거나 상대적일 수 있다.

* `server.tomcat.accesslog.file-date-format` - 로그 파일 이름에 넣을 날짜 형식이다.

* `server.tomcat.accesslog.prefix` - 로그 파일 이름 접두사이다.

* `server.tomcat.accesslog.suffix` - 로그 파일 이름 접미사이다.

* `server.tomcat.accesslog.rotate` - 액세스 로그 회전 활성화 여부이다.

## 3. Java Configuration

[TomcatServletWebServerFactory](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/web/servlet/TomcatServletWebServerFactoryCustomizer.html) 빈도 사용자 정의하여 Tomcat 서버를 사용자 정의할 수 있다.

```java
@Component
public class TomcatCustomizer implements WebServerFactoryCustomizer<TomcatServletWebServerFactory> {

    @Override
    public void customize(TomcatServletWebServerFactory factory) {

        factory.setContextPath("");
        factory.setPort(8080);
    }
}
```

## [출처 및 참고]
* [https://howtodoinjava.com/spring-boot2/embedded-tomcat-configuration/](https://howtodoinjava.com/spring-boot2/embedded-tomcat-configuration/)
