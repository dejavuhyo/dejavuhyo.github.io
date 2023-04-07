---
title: Spring WebClient
author: dejavuhyo
date: 2022-11-21 21:00:00 +0900
categories: [Framework, Spring]
tags: [spring-webclient, webclient, rest-api, http-get, http-post, http-delete, http-put, webclient-특징, webclient-사용]
---

## 1. WebClient란
WebClient 는 웹 요청을 수행하기 위한 기본 진입점을 나타내는 인터페이스이다.

Spring Web Reactive 모듈의 일부로 생성되었으며 이러한 시나리오에서 클래식 RestTemplate을 대체할 것이다. 또한 새 클라이언트는 HTTP/1.1 프로토콜을 통해 작동하는 반응형 비차단 솔루션이다.

실제로 비차단 클라이언트이고 spring-webflux 라이브러리에 속하지만, 이 솔루션은 동기식 및 비동기식 작업을 모두 지원하므로 서블릿 스택에서 실행되는 애플리케이션에도 적합하다.

이는 결과를 얻기 위해 작업을 차단하여 달성할 수 있다. 물론 이 방법은 Reactive Stack에서 작업하는 경우 권장되지 않는다.

인터페이스에는 작업할 단일 구현인 DefaultWebClient 클래스가 있다.

## 2. Dependencies
Spring Boot 애플리케이션을 사용하고 있기 때문에 Spring Framework의 Reactive Web 사용하기 위해 필요한 것은 spring-boot-starter-efflux이다.

### 1) Maven
pom.xml 파일에 다음 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

### 2) Gradle
build.gradle 파일에 다음 종속성을 추가한다.

```gradle
dependencies {
    compile 'org.springframework.boot:spring-boot-starter-webflux'
}
```

## 3. WebClient

### 1) WebClient 인스턴스 생성
세 가지 옵션이 있다. 첫 번째는 기본 설정으로 WebClient 개체를 만드는 것이다.

```java
WebClient client = WebClient.create();
```

두 번째 옵션은 주어진 기본 URI로 WebClient 인스턴스를 시작하는 것이다.

```java
WebClient client = WebClient.create("http://localhost:8080");
```

세 번째 옵션(가장 고급 옵션)은 전체 사용자 지정을 허용하는 DefaultWebClientBuilder 클래스를 사용하여 클라이언트를 빌드하는 것이다.

```java
WebClient client = WebClient.builder()
  .baseUrl("http://localhost:8080")
  .defaultCookie("cookieKey", "cookieValue")
  .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
  .defaultUriVariables(Collections.singletonMap("url", "http://localhost:8080"))
  .build();
```

### 2) 제한 시간이 있는 WebClient 인스턴스 생성
종종 기본 HTTP 시간 초과인 30초는 필요에 비해 너무 느려서 이 동작을 사용자 지정하기 위해 HttpClient 인스턴스를 만들고 이를 사용하도록 WebClient를 구성할 수 있다.

* ChannelOption.CONNECT_TIMEOUT_MILLIS 옵션을 통해 연결 시간 제한을 설정한다.

* 각각 ReadTimeoutHandler 및 WriteTimeoutHandler를 사용하여 읽기 및 쓰기 제한 시간을 설정한다.

* responseTimeout 지시문을 사용하여 응답 시간 제한 구성한다.

HttpClient 인스턴스에 지정되어야 한다.

```java
HttpClient httpClient = HttpClient.create()
  .option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 5000)
  .responseTimeout(Duration.ofMillis(5000))
  .doOnConnected(conn ->
    conn.addHandlerLast(new ReadTimeoutHandler(5000, TimeUnit.MILLISECONDS))
      .addHandlerLast(new WriteTimeoutHandler(5000, TimeUnit.MILLISECONDS)));

WebClient client = WebClient.builder()
  .clientConnector(new ReactorClientHttpConnector(httpClient))
  .build();
```

클라이언트 요청에 대해서도 타임아웃을 호출할 수 있지만 이는 신호 타임아웃이지 HTTP 연결, 읽기/쓰기 또는 응답 타임아웃이 아니다. Mono/Flux 게시자의 시간 초과이다.

### 3) 요청 준비 - 방법 정의
method(HttpMethod method)를 호출하여 요청의 HTTP 메서드를 지정해야 한다.

```java
UriSpec<RequestBodySpec> uriSpec = client.method(HttpMethod.POST);
```

또는 get, post 및 delete와 같은 바로 가기 메서드를 호출한다.

```java
UriSpec<RequestBodySpec> uriSpec = client.post();
```

> 참고: 요청 사양 변수(WebClient.UriSpec, WebClient.RequestBodySpec, WebClient.RequestHeadersSpec, WebClient.ResponseSpec)를 재사용하는 것처럼 보일 수 있지만 이는 단순하게 다른 접근 방식을 제시하기 위한 것이다. 이러한 지시문은 다른 요청에 재사용해서는 안 되며 참조를 검색하므로 후자의 작업은 이전 단계에서 만든 정의를 수정한다.

### 4) 요청 준비 – URL 정의
문자열로 uri API에 전달할 수 있다.

```java
RequestBodySpec bodySpec = uriSpec.uri("/resource");
```

UriBuilder 함수 사용

```java
RequestBodySpec bodySpec = uriSpec.uri(
  uriBuilder -> uriBuilder.pathSegment("/resource").build());
```

또는 java.net.URL 인스턴스

```java
RequestBodySpec bodySpec = uriSpec.uri(URI.create("/resource"));
```

WebClient에 대한 기본 URL을 정의한 경우 이 마지막 메서드가 이 값을 재정의한다.

### 5) 요청 준비 – 본문 정의
필요한 경우 요청 본문, 콘텐츠 유형, 길이, 쿠키 또는 헤더를 설정할 수 있다.

요청 본문을 설정하려는 경우 몇 가지 방법이 있다. 가장 일반적이고 간단한 옵션은 bodyValue 메서드를 사용하는 것이다.

```java
RequestHeadersSpec<?> headersSpec = bodySpec.bodyValue("data");
```

또는 게시자(게시될 요소 유형)를 본문 메서드에 표시

```java
RequestHeadersSpec<?> headersSpec = bodySpec.body(
  Mono.just(new Foo("name")), Foo.class);
```

또는 BodyInserters 유틸리티 클래스를 사용할 수 있다. 예를 들어, bodyValue 메서드에서 했던 것처럼 간단한 객체를 사용하여 요청 본문을 채우는 방법이다.

```java
RequestHeadersSpec<?> headersSpec = bodySpec.body(
  BodyInserters.fromValue("data"));
```

Reactor 인스턴스를 사용하는 경우 `BodyInserters#fromPublisher` 메서드를 사용할 수 있다.

```java
RequestHeadersSpec headersSpec = bodySpec.body(
  BodyInserters.fromPublisher(Mono.just("data")),
  String.class);
```

이 클래스는 고급 시나리오를 다루는 다른 직관적인 기능도 제공한다. 예를 들어 멀티파트 요청을 보내야 하는 경우이다.

```java
LinkedMultiValueMap map = new LinkedMultiValueMap();
map.add("key1", "value1");
map.add("key2", "value2");
RequestHeadersSpec<?> headersSpec = bodySpec.body(
  BodyInserters.fromMultipartData(map));
```

이러한 모든 메서드는 요청 본문으로 제공할 수 있는 BodyInserter 인스턴스를 만든다.

BodyInserter는 주어진 출력 메시지와 삽입 중에 사용되는 컨텍스트로 ReactiveHttpOutputMessage 본문을 채우는 역할을 하는 인터페이스이다.

게시자는 잠재적으로 무한한 수의 순차 요소를 제공하는 반응적 구성 요소이다. 인터페이스이기도 하며 가장 널리 사용되는 구현은 Mono 및 Flux이다.

### 6) 요청 준비 – 헤더 정의
본문을 설정한 후 헤더, 쿠키 및 허용되는 미디어 유형을 설정할 수 있다. 클라이언트를 인스턴스 화할 때 이미 설정된 값에 값이 추가된다.

또한 `If-None-Match`, `If-Modified-Since`, `Accept` 및 `Accept-Charset`과 같이 가장 일반적으로 사용되는 헤더에 대한 추가 지원이 있다.

다음은 이러한 값을 사용하는 방법의 예이다.

```java
ResponseSpec responseSpec = headersSpec.header(
    HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
  .accept(MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML)
  .acceptCharset(StandardCharsets.UTF_8)
  .ifNoneMatch("*")
  .ifModifiedSince(ZonedDateTime.now())
  .retrieve();
```

### 7) 응답 받기
마지막 단계는 요청을 보내고 응답을 받는 것이다. exchangeToMono/exchangeToFlux 또는 검색 방법을 사용하여 받을 수 있다.

exchangeToMono 및 exchangeToFlux 메서드를 사용 하면 상태 및 헤더와 함께 ClientResponse에 액세스할 수 있다.

```java
Mono<String> response = headersSpec.exchangeToMono(response -> {
  if (response.statusCode().equals(HttpStatus.OK)) {
      return response.bodyToMono(String.class);
  } else if (response.statusCode().is4xxClientError()) {
      return Mono.just("Error response");
  } else {
      return response.createException()
        .flatMap(Mono::error);
  }
});
```

검색 방법은 본문을 직접 가져오는 최단 경로이지만 다음과 같다.

```java
Mono<String> response = headersSpec.retrieve()
  .bodyToMono(String.class);
```

ResponseSpec에 주의해야 한다. 상태 코드가 4xx (클라이언트 오류) 또는 5xx (서버 오류)인 경우 WebClientException을 throw 하는 bodyToMono 메서 등이다.

## 4. WebTestClient 작업
WebTestClient는 WebFlux 서버 endpoints를 테스트하기 위한 기본 진입점이다. WebClient와 매우 유사한 API를 가지고 있으며 대부분의 작업을 주로 테스트 컨텍스트 제공에 중점을 둔 내부 WebClient 인스턴스에 위임한다. DefaultWebTestClient 클래스는 단일 인터페이스 구현이다.

### 1) 서버에 바인딩
실행 중인 서버에 대한 실제 요청으로 종단 간 통합 테스트를 완료하려면 bindToServer 메서드를 사용할 수 있다.

```java
WebTestClient testClient = WebTestClient
  .bindToServer()
  .baseUrl("http://localhost:8080")
  .build();
```

### 2) 라우터에 바인딩
특정 RouterFunction을 bindToRouterFunction 메서드에 전달하여 테스트할 수 있다.

```java
RouterFunction function = RouterFunctions.route(
  RequestPredicates.GET("/resource"),
  request -> ServerResponse.ok().build()
);

WebTestClient
  .bindToRouterFunction(function)
  .build().get().uri("/resource")
  .exchange()
  .expectStatus().isOk()
  .expectBody().isEmpty();
```

### 3) 웹 핸들러에 바인딩
WebHandler 인스턴스를 사용하는 bindToWebHandler 메서드를 사용하여 동일한 동작을 수행할 수 있다.

```java
WebHandler handler = exchange -> Mono.empty();
WebTestClient.bindToWebHandler(handler).build();
```

### 4) 애플리케이션 컨텍스트에 바인딩
ApplicationContext를 취하고 컨트롤러 bean 및 `@EnableWebFlux` 구성에 대한 컨텍스트를 분석한다.

ApplicationContext 인스턴스를 주입하면 간단한 코드 스니펫이 다음과 같이 표시될 수 있다.

```java
@Autowired
private ApplicationContext context;

WebTestClient testClient = WebTestClient.bindToApplicationContext(context)
  .build();
```

### 5) 컨트롤러에 바인딩
더 짧은 접근 방식은 bindToController 메서드로 테스트하려는 컨트롤러 배열을 제공하는 것이다. 컨트롤러 클래스가 있고 필요한 클래스에 주입했다고 가정하면 다음과 같이 작성할 수 있다.

```java
@Autowired
private Controller controller;

WebTestClient testClient = WebTestClient.bindToController(controller).build();
```

### 6) 요청
WebTestClient 개체를 빌드한 후 체인의 모든 다음 작업은 exchange 메서드(응답을 받는 한 가지 방법)까지 WebClient와 유사하다. 이 메서드는 WebTestClient.ResponseSpec 인터페이스를 제공하여 expectStatus, expectBody와 같은 유용한 메서드와 함께 작동한다.

```java
WebTestClient
  .bindToServer()
    .baseUrl("http://localhost:8080")
    .build()
    .post()
    .uri("/resource")
  .exchange()
    .expectStatus().isCreated()
    .expectHeader().valueEquals("Content-Type", "application/json")
    .expectBody().jsonPath("field").isEqualTo("value");
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-5-webclient](https://www.baeldung.com/spring-5-webclient)
