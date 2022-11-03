---
title: Spring Cloud Gateway
author: dejavuhyo
date: 2021-12-30 09:30:00 +0900
categories: [Application, Architecture]
tags: [spring-cloud-gateway, api-gateway, gateway, scg, spring-cloud, 게이트웨이, api-게이트웨이]
---

## 1. Spring Cloud Gateway(SCG)란
Spring Cloud Gateway(SCG)란 MSA 환경에서 사용하는 API Gateway중 하나로 Spring5, Spring Boot2, Project Reactor로 구축된 API Gateway다. Spring Cloud Gateway는 API 라우팅 및 보안, 모니터링/메트릭 등의 기능을 간단하고 효과적인 방법으로 제공한다.

## 2. 용어

* **Route:** 게이트웨이의 기본 빌딩 블록이다. ID, 대상 URI, 술어 모음 및 필터 모음으로 정의된다. 집계 조건자가 true이면 경로가 일치한다.

* **Predicate:** 이것은 Java 8 기능 술어 이다. 입력 유형은 [Spring Framework ServerWebExchange](https://docs.spring.io/spring-framework/docs/5.0.x/javadoc-api/org/springframework/web/server/ServerWebExchange.html) 이다. 이를 통해 헤더 또는 매개변수와 같은 HTTP 요청의 모든 항목과 일치시킬 수 있다.

* **Filter:** 특정 팩토리로 구축된 Spring Framework의 GatewayFilter 인스턴스이다. 여기에서 다운스트림 요청을 보내기 전이나 후에 요청과 응답을 수정할 수 있다.

## 3. 작동 원리
다음 다이어그램은 Spring Cloud Gateway 작동 방식에 대한 상위 수준 개요를 제공한다.

![spring-cloud-gateway-diagram](/assets/img/2021-12-30-spring-cloud-gateway/spring-cloud-gateway-diagram.png)

클라이언트는 Spring Cloud Gateway에 요청한다. 게이트웨이 처리기 매핑에서 요청이 라우트와 일치하는 것으로 확인되면 게이트웨이 웹 처리기로 전송된다.

이 핸들러는 요청에 특정한 필터 체인을 통해 요청을 실행한다. 필터가 점선으로 구분되는 이유는 필터가 프록시 요청이 전송되기 전후에 로직을 실행할 수 있기 때문이다.

모든 "pre" 필터 논리가 실행된다. 그런 다음 프록시 요청이 이루어진다. 프록시 요청이 이루어진 후 "post" 필터 로직이 실행된다.

> 포트가 없는 경로에 정의된 URI는 HTTP 및 HTTPS URI에 대해 각각 80 및 443의 기본 포트 값을 얻는다.

## 4. Spring Cloud Zuul과 Spring Cloud Gateway의 차이점과 특징

### 1) Blockinga과 non-Blocking
Blocking 방식은 요청을 보내고 응답이 올때까지 다음으로 진행하지 않고 기다린다.

non-Blocking 방식은 요청을 보내고 바로 다음으로 진행하여 다른 일을 하다가, 응답이 오면 그에 맞는 처리를 한다.

### 2) Filter only과 Predicates + Filters
Zuul은 Filter 만으로 동작하는 반면에, SCG는 Predicates(수행을 위한 사전 요구조건)와 Filter를 조합하여 동작한다.

![zuul](/assets/img/2021-12-30-spring-cloud-gateway/zuul.png)

![spring-cloud-gateway](/assets/img/2021-12-30-spring-cloud-gateway/spring-cloud-gateway.png)

### 3) Tomcat과 Netty
Zuul은 Web/WAS로 Tomcat을 사용하고, SCG는 Netty를 사용한다.

Netty는 비동기 네트워킹을 지원하는 어플리케이션 프레임워크이다.

## [출처 및 참고]
* <https://cloud.spring.io/spring-cloud-gateway/reference/html/>
* <https://twofootdog.tistory.com/64>
* <https://cheese10yun.github.io/spring-cloud-gateway/>
* <https://happycloud-lee.tistory.com/218>
