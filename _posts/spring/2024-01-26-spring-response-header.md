---
title: Spring Response Header 설정
author: dejavuhyo
date: 2024-01-26 09:15:00 +0900
categories: [Framework, Spring]
tags: [spring-response-header, response-header, spring-response, spring-header, 응답-헤더, 스프링-헤더]
---

## 1. Non-Reactive Components 헤더
단일 응답에 헤더를 설정하려면 `HttpServletResponse` 또는 `ResponseEntity` 객체를 사용할 수 있다.

반대로, 모든 응답 또는 여러 응답에 필터를 추가하려면 `Filter`를 구성해야 한다 .

### 1) HttpServletResponse 사용
`HttpServletResponse` 객체를 REST 엔드포인트에 인수로 추가한 다음 `addHeader()` 메서드를 사용하면 된다.

```java
@GetMapping("/http-servlet-response")
public String usingHttpServletResponse(HttpServletResponse response) {
    response.addHeader("Baeldung-Example-Header", "Value-HttpServletResponse");
    return "Response with header using HttpServletResponse";
}
```

위의 예에서 볼 수 있듯이 응답 개체를 반환할 필요는 없다.

### 2) ResponseEntity 사용
이 경우 `ResponseEntity` 클래스에서 제공하는 BodyBuilder를 사용한다.

```java
@GetMapping("/response-entity-builder-with-http-headers")
public ResponseEntity<String> usingResponseEntityBuilderAndHttpHeaders() {
    HttpHeaders responseHeaders = new HttpHeaders();
    responseHeaders.set("Baeldung-Example-Header", 
      "Value-ResponseEntityBuilderWithHttpHeaders");

    return ResponseEntity.ok()
      .headers(responseHeaders)
      .body("Response with header using ResponseEntity");
}
```

HttpHeaders 클래스는 가장 일반적인 헤더를 설정하는데 편리한 여러 가지 메서드를 제공한다.

> 참조: [https://github.com/eugenp/tutorials/tree/master/spring-web-modules/spring-rest-http](https://github.com/eugenp/tutorials/tree/master/spring-web-modules/spring-rest-http)

### 3) 모든 응답에 대한 헤더 추가
특정 헤더를 여러 엔드포인트에 설정한다고 가정한다.

물론 각 매핑 방법에 대해 이전 코드를 복제해야 한다면 답답할 것이다.

더 나은 접근 방식은 서비스에서 필터를 구성하는 것이다.

```java
@WebFilter("/filter-response-header/*")
public class AddResponseHeaderFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, 
      FilterChain chain) throws IOException, ServletException {
        HttpServletResponse httpServletResponse = (HttpServletResponse) response;
        httpServletResponse.setHeader(
          "Baeldung-Example-Filter-Header", "Value-Filter");
        chain.doFilter(request, response);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // ...
    }

    @Override
    public void destroy() {
        // ...
    }
}
```

`@WebFilter` 주석을 사용하면 이 필터가 적용될 `urlPattern`을 나타낼 수 있다.

Spring에서 필터를 검색할 수 있도록 하려면 Spring Application 클래스에 `@ServletComponentScan` 주석을 추가해야 한다.

```java
@ServletComponentScan
@SpringBootApplication
public class ResponseHeadersApplication {

    public static void main(String[] args) {
        SpringApplication.run(ResponseHeadersApplication.class, args);
    }
}
```

`@WebFilter`가 제공하는 기능이 필요하지 않은 경우 대신 Filter 클래스에서 `@Component` 주석을 사용하여 이 마지막 단계를 피할 수 있다.

## 2. Reactive Endpoints 헤더
`ServerHttpResponse`, `ResponseEntity` 또는 `ServerResponse`(functional endpoints) 클래스 및 인터페이스를 사용하여 단일 엔드포인트 응답에 헤더를 설정하는 방법과 모든 응답에 헤더를 추가하기 위해 Spring WebFilter를 구현하는 방법도 알아본다.

### 1) ServerHttpResponse 사용
이 접근 방식은 `HttpServletResponse` 대응 방식과 매우 유사하다.

```java
@GetMapping("/server-http-response")
public Mono<String> usingServerHttpResponse(ServerHttpResponse response) {
    response.getHeaders().add("Baeldung-Example-Header", "Value-ServerHttpResponse");
    return Mono.just("Response with header using ServerHttpResponse");
}
```

### 2) ResponseEntity 사용
non-reactive 엔드포인트에서와 마찬가지로 ResponseEntity 클래스를 사용할 수 있다.

```java
@GetMapping("/response-entity")
public Mono<ResponseEntity<String>> usingResponseEntityBuilder() {
    String responseHeaderKey = "Baeldung-Example-Header";
    String responseHeaderValue = "Value-ResponseEntityBuilder";
    String responseBody = "Response with header using ResponseEntity (builder)";

    return Mono.just(ResponseEntity.ok()
      .header(responseHeaderKey, responseHeaderValue)
      .body(responseBody));
}
```

### 3) ServerResponse 사용
마지막에 나온 클래스와 인터페이스는 `@Controller` 주석이 달린 클래스에서 사용할 수 있지만 새로운 [Spring 5 Functional Web Framework](https://www.baeldung.com/spring-5-functional-web)에는 적합하지 않다.

HandlerFunction에 헤더를 설정하려면 ServerResponse 인터페이스를 사용해야 한다.

```java
public Mono<ServerResponse> useHandler(final ServerRequest request) {
     return ServerResponse.ok()
        .header("Baeldung-Example-Header", "Value-Handler")
        .body(Mono.just("Response with header using Handler"),String.class);
}
```

### 4) 모든 응답에 대한 헤더 추가
Spring 5는 서비스가 검색한 모든 응답에 헤더를 설정하는 WebFilter 인터페이스를 제공한다.

```java
@Component
public class AddResponseHeaderWebFilter implements WebFilter {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, WebFilterChain chain) {
        exchange.getResponse()
          .getHeaders()
          .add("Baeldung-Example-Filter-Header", "Value-Filter");
        return chain.filter(exchange);
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-response-header](https://www.baeldung.com/spring-response-header)
