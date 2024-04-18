---
title: Spring Boot Tomcat 구성
author: dejavuhyo
date: 2024-04-18 13:47:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-tomcat, embedded-tomcat, spring-tomcat]
---

## 1. 일반적인 임베디드 Tomcat 구성

### 1) 서버 주소 및 포트
변경하려는 가장 일반적인 구성은 포트 번호이다.

```properties
server.port=80
```

`server.port` 매개변수를 제공하지 않으면 기본적으로 8080으로 설정된다.

어떤 경우에는 서버가 바인딩되어야 하는 네트워크 주소를 설정하고 싶을 수도 있다. 즉, 서버가 수신할 IP 주소를 정의한다.

```properties
server.address=my_custom_ip
```

기본적으로 값은 0.0.0.0으로 설정되어 있으며, 이는 모든 IPv4 주소를 통한 연결을 허용한다. 예를 들어, localhost(`127.0.0.1`)와 같은 다른 값을 설정하면 서버가 더 선택적으로 만들어진다.

### 2) 오류 처리
기본적으로 Spring Boot는 표준 오류 웹 페이지를 제공한다. 이 페이지를 화이트라벨 이라고 한다. 기본적으로 활성화되어 있지만 오류 정보를 표시하지 않으려면 비활성화할 수 있다.

```properties
server.error.whitelabel.enabled=false
```

화이트라벨의 기본 경로는 `/error`이다. `server.error.path` 매개변수를 설정하여 이를 사용자 정의할 수 있다.

```properties
server.error.path=/user-error
```

또한 오류에 대해 어떤 정보가 표시되는지 결정하는 속성을 설정할 수도 있다. 예를 들어 오류 메시지와 스택 추적을 포함할 수 있다.

```properties
server.error.include-exception=true
server.error.include-stacktrace=always
```

REST를 위한 [예외 메시지 처리](https://www.baeldung.com/exception-handling-for-rest-with-spring) 및 [화이트라벨 오류 페이지](https://www.baeldung.com/spring-boot-custom-error-page) 사용자 정의 튜토리얼에서는 Spring Boot의 오류 처리에 대해 자세히 설명한다.

### 3) 서버 연결
리소스가 적은 컨테이너에서 실행하는 경우 CPU 및 메모리 로드를 줄이는 것이 좋다. 이를 수행하는 한 가지 방법은 애플리케이션에서 처리할 수 있는 동시 요청 수를 제한하는 것이다. 반대로, 더 나은 성능을 얻기 위해 더 많은 사용 가능한 리소스를 사용하도록 이 값을 늘릴 수 있다.

Spring Boot에서는 Tomcat 작업자 스레드의 최대 양을 정의할 수 있다.

```properties
server.tomcat.threads.max=200
```

웹 서버를 구성할 때 서버 연결 시간 제한을 설정하는 것도 유용할 수 있다. 이는 연결 후 연결이 닫히기 전에 클라이언트가 요청을 할 때까지 서버가 기다리는 최대 시간을 나타낸다.

```properties
server.connection-timeout=5s
```

요청 헤더의 최대 크기를 정의할 수도 있다.

```properties
server.max-http-header-size=8KB
```

요청 본문의 최대 크기 설정이다.

```properties
server.tomcat.max-swallow-size=2MB
```

또는 전체 게시물 요청의 최대 크기 설정이다.

```properties
server.tomcat.max-http-post-size=2MB
```

### 4) SSL
Spring Boot 애플리케이션에서 SSL 지원을 활성화하려면 `server.ssl.enabled` 속성을 true로 설정 하고 SSL 프로토콜을 정의해야 한다.

```properties
server.ssl.enabled=true
server.ssl.protocol=TLS
```

또한 인증서를 보유하는 키 저장소에 대한 비밀번호, 유형 및 경로를 구성해야 한다.

```properties
server.ssl.key-store-password=my_password
server.ssl.key-store-type=keystore_type
server.ssl.key-store=keystore-path
```

그리고 키 저장소에서 키를 식별하는 별칭도 정의해야 한다.

```properties
server.ssl.key-alias=tomcat
```

SSL 구성에 대한 내용은 [Spring Boot 문서에서 자체 서명 인증서를 사용하는 HTTPS](https://www.baeldung.com/spring-boot-https-self-signed-certificate)를 참조한다.

### 5) Tomcat 서버 액세스 로그
Tomcat 액세스 로그는 페이지 적중 횟수, 사용자 세션 활동 등을 측정할 때 유용하다.

액세스 로그를 활성화하려면 다음을 설정하면 된다.

```properties
server.tomcat.accesslog.enabled=true
```

또한 로그 파일에 추가되는 디렉터리 이름, 접두사, 접미사 및 날짜 형식과 같은 다른 매개 변수도 구성해야 한다.

```properties
server.tomcat.accesslog.directory=logs
server.tomcat.accesslog.file-date-format=yyyy-MM-dd
server.tomcat.accesslog.prefix=access_log
server.tomcat.accesslog.suffix=.log
```

## 2. 임베디드 Tomcat 버전
`application.properties` 파일을 구성하여 사용 중인 Tomcat 버전을 변경할 수 없다. 좀 더 복잡하며 주로 `spring-boot-starter-parent`를 사용하는지 여부에 따라 달라진다.

그러나 진행하기 전에 각 Spring Boot 릴리스가 특정 Tomcat 버전에 대해 설계되고 테스트되었다는 점을 알아야 한다. 이를 변경하면 예상치 못한 호환성 문제가 발생할 수 있다.

### 1) spring-boot-starter-parent 사용
Maven을 사용하고 `spring-boot-starter-parent`에서 상속하도록 프로젝트를 구성하면 `pom.xml`의 특정 속성을 덮어써서 개별 종속성을 재정의할 수 있다.

이를 염두에 두고 Tomcat 버전을 업데이트하려면 `tomcat.version` 속성을 사용해야 한다.

```xml
<properties>
    <tomcat.version>9.0.44</tomcat.version>
</properties>
```

### 2) pring-boot-dependencies 사용
`spring-boot-starter-parent`를 사용하고 싶지 않거나 사용할 수 없는 상황이 있다. 예를 들어 Spring Boot 프로젝트에서 사용자 정의 상위 항목을 사용하는 경우이다. 이러한 경우 종속성 관리의 이점을 계속 활용하기 위해 `spring-boot-dependent`를 사용할 가능성이 크다.

그러나 이 설정에서는 이전 섹션에 표시된 대로 Maven 속성을 사용하여 개별 종속성을 재정의할 수 없다.

동일한 목표를 달성하고 계속해서 다른 Tomcat 버전을 사용하려면 pom 파일의 dependencyManagement 섹션에 항목을 추가해야 한다. 중요한 점은 `spring-boot-dependencies` 앞에 배치해야 한다는 것이다.

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-core</artifactId>
            <version>9.0.44</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.4.5</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-configure-tomcat](https://www.baeldung.com/spring-boot-configure-tomcat)
