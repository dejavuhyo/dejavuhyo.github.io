---
title: Spring MVC 및 Spring Security를 ​​통한 Servlet 3 비동기
author: dejavuhyo
date: 2023-10-04 20:55:00 +0900
categories: [Framework, Spring]
tags: [spring-async, spring-security, async-security]
---

## 1. 메이븐 의존성
Spring MVC에서 비동기 통합을 사용하려면 `pom.xml`에 다음 종속성을 포함해야 한다.

```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>5.7.3</version>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>5.7.3</version>
</dependency>
```

## 2. 스프링 MVC와 @Async
[공식 문서](https://spring.io/blog/2012/12/17/spring-security-3-2-m1-highlights-servlet-3-api-support#servlet3-async)에 따르면 Spring Security는 [WebAsyncManager](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/request/async/WebAsyncManager.html)와 통합된다.

첫 번째 단계는 springSecurityFilterChain이 비동기 요청을 처리하도록 설정되었는지 확인하는 것이다. Servlet 구성 클래스에 다음 행을 추가하여 Java 구성에서 이를 수행할 수 있다.

```java
dispatcher.setAsyncSupported(true);
```

또는 XML 구성이다.

```xml
<filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
    <async-supported>true</async-supported>
</filter>
<filter-mapping>
    <filter-name>springSecurityFilterChain</filter-name>
    <url-pattern>/*</url-pattern>
    <dispatcher>REQUEST</dispatcher>
    <dispatcher>ASYNC</dispatcher>
</filter-mapping>
```

또한 서블릿 구성에서 비동기 지원 매개변수를 활성화해야 한다.

```xml
<servlet>
    ...
    <async-supported>true</async-supported>
    ...
</servlet>
```

이제 SecurityContext가 전파된 비동기 요청을 보낼 준비가 되었다.

Spring Security 내의 내부 메커니즘은 다른 스레드에서 응답이 커밋되어 사용자 로그아웃이 발생할 때 SecurityContext가 더 이상 지워지지 않도록 보장한다.

## 3. 사용 사례
간단한 예를 통해 이를 실제로 확인한다.

```java
@Override
public Callable<Boolean> checkIfPrincipalPropagated() {
    Object before 
      = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
    log.info("Before new thread: " + before);

    return new Callable<Boolean>() {
        public Boolean call() throws Exception {
            Object after 
              = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
            log.info("New thread: " + after);
            return before == after;
        }
    };
}
```

Spring SecurityContext가 새 스레드로 전파되는지 확인한다.

위에 제시된 메서드는 로그에 표시된 것처럼 SecurityContext가 포함된 Callable을 자동으로 실행 한다.

```text
web - 2017-01-02 10:42:19,011 [http-nio-8081-exec-3] INFO
  o.baeldung.web.service.AsyncService - Before new thread:
  org.springframework.security.core.userdetails.User@76507e51:
  Username: temporary; Password: [PROTECTED]; Enabled: true;
  AccountNonExpired: true; credentialsNonExpired: true;
  AccountNonLocked: true; Granted Authorities: ROLE_ADMIN

web - 2017-01-02 10:42:19,020 [MvcAsync1] INFO
  o.baeldung.web.service.AsyncService - New thread:
  org.springframework.security.core.userdetails.User@76507e51:
  Username: temporary; Password: [PROTECTED]; Enabled: true;
  AccountNonExpired: true; credentialsNonExpired: true;
  AccountNonLocked: true; Granted Authorities: ROLE_ADMIN
```

전파될 SecurityContext를 설정하지 않으면 두 번째 요청은 null 값으로 종료된다.

전파된 SecurityContext와 함께 비동기 요청을 사용하는 다른 중요한 사용 사례도 있다.

* 병렬로 실행될 수 있고 실행하는데 상당한 시간이 걸릴 수 있는 여러 외부 요청을 만들고 싶다.

* 로컬에서 수행해야 할 중요한 처리가 있고 외부 요청이 해당 처리와 동시에 실행될 수 있다.

* 다른 것들은 예를 들어 이메일 보내기와 같은 실행 후 잊어버리는 시나리오를 나타낸다.

여러 메서드 호출이 이전에 동기 방식으로 함께 연결된 경우 이를 비동기 방식으로 변환하려면 결과 동기화가 필요할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-async-security](https://www.baeldung.com/spring-mvc-async-security)
