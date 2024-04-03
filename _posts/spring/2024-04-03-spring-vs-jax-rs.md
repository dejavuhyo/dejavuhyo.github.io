---
title: Spring vs JAX-RS 
author: dejavuhyo
date: 2024-04-03 11:52:00 +0900
categories: [Framework, Spring]
tags: [spring-rest-api, spring, jax-rs, rest-api]
---

## 1. Jakarta RESTful Web Services
JAVA EE 세계의 일부가 되려면 기능에 사양, 호환 가능한 구현 및 [TCK](https://en.wikipedia.org/wiki/Technology_Compatibility_Kit)가 있어야 한다. 따라서 JAX-RS는 REST 서비스 구축을 위한 사양 집합이다. 가장 잘 알려진 참조 구현은 [RESTEasy](https://www.baeldung.com/resteasy-tutorial) 및 [Jersey](https://www.baeldung.com/jersey-rest-api-with-spring) 이다.

이제 간단한 컨트롤러를 구현하여 Jersey를 확인한다.

```java
@Path("/hello")
public class HelloController {

    @GET
    @Path("/{name}")
    @Produces(MediaType.TEXT_PLAIN)
    public Response hello(@PathParam("name") String name) {
        return Response.ok("Hello, " + name).build();
    }

}
```

위에서 엔드포인트는 `@Produces` 주석이 명시한 대로 간단한 "text/plain" 응답을 반환한다. 특히, 두 개의 `@Path` 주석이 있는 name이라는 매개변수를 허용하는 hello HTTP 리소스를 노출하고 있다. 또한 `@GET` 주석을 사용하여 GET 요청 임을 지정해야 한다.

## 2. Spring MVC를 사용한 REST
Spring MVC는 웹 애플리케이션을 생성하기 위한 Spring Framework의 모듈이다. Spring Framework에 REST 기능을 추가한다.

Spring MVC를 사용하여 위와 동일한 GET 요청 예제를 만든다.

```java
@RestController
@RequestMapping("/hello")
public class HelloController {

    @GetMapping(value = "/{name}", produces = MediaType.TEXT_PLAIN_VALUE)
    public ResponseEntity<?> hello(@PathVariable String name) {
        return new ResponseEntity<>("Hello, " + name, HttpStatus.OK);
    }

}
```

코드를 보면 `@RequestMapping`은 hello HTTP 리소스를 다루고 있다. 특히 `@GetMapping` 주석을 통해 GET 요청 임을 지정하고 있다. name 이라는 매개변수를 받아들이고 "text/plain" 응답을 반환한다.

## 3. 차이점
JAX-RS는 Java 주석 세트를 제공하고 이를 일반 Java 객체에 적용하는데 달려 있다. 실제로 이러한 주석은 클라이언트-서버 통신의 하위 수준 세부 정보를 추상화하는데 도움이 된다. 구현을 단순화하기 위해 HTTP 요청 및 응답을 처리하고 코드에 바인딩하는 주석을 제공한다. JAX-RS는 사양일 뿐이며 사용하려면 호환 가능한 구현이 필요하다.

반면에 Spring MVC는 REST 기능을 갖춘 완전한 프레임워크이다. JAX-RS와 마찬가지로 낮은 수준의 세부 정보를 추상화하는데 유용한 주석도 제공한다. 주요 이점은 Spring Framework 생태계의 일부라는 것이다. 따라서 다른 Spring 모듈처럼 종속성 주입을 사용할 수 있다. 또한 Spring AOP, Spring Data REST 및 Spring Security와 같은 다른 구성 요소와 쉽게 통합된다.

## [출처 및 참고]
* [https://www.baeldung.com/rest-api-jax-rs-vs-spring#jakarta-restful-web-services](https://www.baeldung.com/rest-api-jax-rs-vs-spring#jakarta-restful-web-services)
