---
title: Spring Cloud Gateway
author: dejavuhyo
date: 2024-03-29 10:28:00 +0900
categories: [Framework, Spring]
tags: [spring-cloud-gateway, spring-gateway, gateway, 스프링-클라우드-게이트웨이, 스프링-게이트웨이, 게이트웨이]
---

## 1. Routing Handler
요청 라우팅에 중점을 두고 Spring Cloud 게이트웨이는 특정 경로와 일치하는 요청에 대해 수행해야 할 작업을 결정하는 게이트웨이 처리기 매핑으로 요청을 전달한다.

Gateway Handler가 RouteLocator를 사용하여 경로 구성을 확인하는 방법에 대한 간단한 예이다.

```java
@Bean
public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
    return builder.routes()
      .route("r1", r -> r.host("**.baeldung.com")
        .and()
        .path("/baeldung")
        .uri("http://baeldung.com"))
      .route(r -> r.host("**.baeldung.com")
        .and()
        .path("/myOtherRouting")
        .filters(f -> f.prefixPath("/myPrefix"))
        .uri("http://othersite.com")
        .id("myOtherID"))
    .build();
}
```

이 API의 주요 구성 요소 활용이다

* Route - 게이트웨이의 기본 API이다. 이는 주어진 식별(ID), 대상(URI) 및 조건자 및 필터 세트로 정의된다.

* Predicate - Java 8 Predicate - 헤더, 메소드 또는 매개변수를 사용하여 HTTP 요청을 일치시키는데 사용된다.

* Filter - 표준 Spring WebFilter

## 2. Dynamic Routing
[Zuul](https://www.baeldung.com/spring-rest-with-zuul-proxy)과 마찬가지로 Spring Cloud Gateway는 요청을 다른 서비스로 라우팅하는 수단을 제공한다.

라우팅 구성은 순수 Java를 사용하거나 속성 구성을 사용하여 생성할 수 있다.

```yml
spring:
  application:
    name: gateway-service
  cloud:
    gateway:
      routes:
      - id: baeldung
        uri: baeldung.com
      - id: myOtherRouting
        uri: localhost:9999
```

## 3. Routing Factories
Spring Cloud Gateway는 Spring WebFlux HandlerMapping 인프라를 사용하여 경로를 일치시킨다.

또한 내장된 Route Predicate Factory도 많이 포함되어 있다. 이러한 모든 조건자는 HTTP 요청의 다양한 속성과 일치한다. 여러 경로 조건자 팩토리는 논리적 "and"를 통해 결합될 수 있다.

경로 일치는 프로그래밍 방식과 다른 유형의 경로 조건자 팩토리를 사용하는 구성 속성 파일을 통해 적용할 수 있다.

> 참고: [Spring Cloud Gateway Routing Predicate Factory](https://www.baeldung.com/spring-cloud-gateway-routing-predicate-factories)

## 4. WebFilter Factories
경로 필터를 사용하면 들어오는 HTTP 요청이나 나가는 HTTP 응답을 수정할 수 있다.

Spring Cloud Gateway에는 많은 내장 WebFilter 팩토리가 포함되어 있으며 사용자 정의 필터를 생성할 수도 있다.

> 참고: [Spring Cloud Gateway WebFilter Factory](https://www.baeldung.com/spring-cloud-gateway-webfilter-factories)

## 5. Spring Cloud Discovery클라이언트 지원
Spring Cloud Gateway는 Eureka Server 및 Consul과 같은 서비스 검색 및 레지스트리 라이브러리와 쉽게 통합될 수 있다.

```java
@Configuration
@EnableDiscoveryClient
public class GatewayDiscoveryConfiguration {

    @Bean
    public DiscoveryClientRouteDefinitionLocator 
      discoveryClientRouteLocator(DiscoveryClient discoveryClient) {
 
        return new DiscoveryClientRouteDefinitionLocator(discoveryClient);
    }
}
```

### 1) LoadBalancer클라이언트 필터
LoadBalancerClientFilter는 `ServerWebExchangeUtils.GATEWAY_REQUEST_URL_ATTR`을 사용하여 교환 속성 속성에서 URI를 찾는다.

URL에 lb 체계(예: `lb://baeldung-service`)가 있는 경우 Spring Cloud LoadBalancerClient를 사용하여 이름(예: baeldung-service)을 실제 호스트 및 포트로 확인한다.

수정되지 않은 원래 URL은 `ServerWebExchangeUtils.GATEWAY_ORIGINAL_REQUEST_URL_ATTR` 속성에 배치된다.

## 6. 모니터링
Spring Cloud Gateway는 애플리케이션 모니터링을 위한 여러 가지 기본 서비스를 제공하는 잘 알려진 Spring Boot 라이브러리인 Actuator API를 사용한다.

Actuator API가 설치 및 구성되면 `/gateway/`에 액세스하여 게이트웨이 모니터링 기능을 시각화할 수 있다.

## 7. 구현
이제 경로 조건자를 사용하여 Spring Cloud Gateway를 프록시 서버로 사용하는 간단한 예이다.

### 1) 종속성
Spring Cloud Gateway는 현재 버전 4.1.1의 마일스톤 저장소에 있다.

프로젝트를 추가하기 위해 모든 필수 종속성을 가져오는 spring-cloud-starter-gateway 종속성을 사용한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
    <version>4.1.1</version>
</dependency>
```

### 2) 코드 구현
이제 `application.yml` 파일에 간단한 라우팅 구성을 만든다.

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: baeldung_route
        uri: http://baeldung.com
        predicates:
        - Path=/baeldung/
management:
  endpoints:
    web:
      exposure:
        include: "*'
```

게이트웨이 애플리케이션 코드는 다음과 같다.

```java
@SpringBootApplication
public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

애플리케이션이 시작된 후 `http://localhost/actuator/gateway/routes/baeldung_route` URL에 액세스하여 생성된 모든 라우팅 구성을 확인할 수 있다.

```java
{
    "id":"baeldung_route",
    "predicates":[{
        "name":"Path",
        "args":{"_genkey_0":"/baeldung"}
    }],
    "filters":[],
    "uri":"http://baeldung.com",
    "order":0
}
```

상대 URL `/baeldung`이 경로로 구성되어 있는 것을 볼 수 있다. 따라서 URL `http://localhost/baeldung`을 입력하면 예제에서 구성한 대로 `http://baeldung.com`으로 리디렉션된다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-cloud-gateway](https://www.baeldung.com/spring-cloud-gateway)
