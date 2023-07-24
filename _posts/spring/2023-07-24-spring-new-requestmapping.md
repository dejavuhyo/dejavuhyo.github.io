---
title: Spring 새로운 RequestMapping
author: dejavuhyo
date: 2023-07-24 09:00:00 +0900
categories: [Framework, Spring]
tags: [spring-requestmapping, new-requestmapping, getmapping, postmappiong, putmapping, deletemapping, patchmapping]
---

## 1. 새로운 주석
일반적으로 전통적인 `@RequestMapping` 주석을 사용하여 URL 핸들러를 구현하려는 경우 다음과 같을 것이다.

```java
@RequestMapping(value = "/get/{id}", method = RequestMethod.GET)
```

새로운 접근 방식을 사용하면 이를 간단히 다음과 같이 줄일 수 있다.

```java
@GetMapping("/get/{id}")
```

Spring은 현재 GET, POST, PUT, DELETE 및 PATCH 와 같은 다양한 유형의 수신 HTTP 요청 메소드를 처리하기 위한 5가지 유형의 내장 주석을 지원한다. 이러한 주석은 다음과 같다.

* `@GetMapping`

* `@PostMapping`

* `@PutMapping`

* `@DeleteMapping`

* `@PatchMapping`

명명 규칙에서 우리는 각 주석이 각각의 들어오는 요청 메서드 유형을 처리하기 위한 것임을 알 수 있다. 즉, `@GetMapping`은 요청 메서드의 GET 유형을 처리하는데 사용되고 `@PostMapping`은 요청 메서드의 POST 유형을 처리하는 데 사용된다.

## 2. 작동 방식
위의 모든 주석은 `@RequestMapping` 및 메서드 요소의 해당 값으로 이미 내부적으로 주석이 달려 있다.

예를 들어 `@GetMapping` 주석의 소스 코드를 살펴보면 다음과 같은 방식으로 이미 `RequestMethod.GET` 주석이 추가되어 있음을 알 수 있다.

```java
@Target({ java.lang.annotation.ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
@RequestMapping(method = { RequestMethod.GET })
public @interface GetMapping {
    // abstract codes
}
```

다른 모든 주석은 동일한 방식으로 생성된다. 즉, `@PostMapping`에는 `RequestMethod.POST`로 주석이 추가되고, `@PutMapping`에는 `RequestMethod.PUT`으로 주석이 추가된다.

## 3. 시행
주석을 사용하여 빠른 REST 애플리케이션을 빌드해본다.

Maven을 사용하여 프로젝트를 빌드하고 Spring MVC를 사용하여 애플리케이션을 생성하므로 `pom.xml`에 필요한 종속성을 추가해야 한다.

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.2.RELEASE</version>
</dependency>
```

최신 버전의 spring-webmvc는 [Central Maven Repository](https://central.sonatype.com/artifact/org.springframework/spring-webmvc)에서 사용할 수 있다.

이제 들어오는 요청 URL을 매핑하기 위해 컨트롤러를 만들어야 한다. 이 컨트롤러 내에서 이러한 모든 주석을 하나씩 사용한다.

### 1) @GetMapping

```java
@GetMapping("/get")
public @ResponseBody ResponseEntity<String> get() {
    return new ResponseEntity<String>("GET Response", HttpStatus.OK);
}
```

```java
@GetMapping("/get/{id}")
public @ResponseBody ResponseEntity<String> getById(@PathVariable String id) {
    return new ResponseEntity<String>("GET Response : " + id, HttpStatus.OK);
}
```

### 2) @PostMapping

```java
@PostMapping("/post")
public @ResponseBody ResponseEntity<String> post() {
    return new ResponseEntity<String>("POST Response", HttpStatus.OK);
}
```

### 3) @PutMapping

```java
@PutMapping("/put")
public @ResponseBody ResponseEntity<String> put() {
    return new ResponseEntity<String>("PUT Response", HttpStatus.OK);
}
```

### 4) @DeleteMapping

```java
@DeleteMapping("/delete")
public @ResponseBody ResponseEntity<String> delete() {
    return new ResponseEntity<String>("DELETE Response", HttpStatus.OK);
}
```

### 5) @PatchMapping

```java
@PatchMapping("/patch")
public @ResponseBody ResponseEntity<String> patch() {
    return new ResponseEntity<String>("PATCH Response", HttpStatus.OK);
}
```

## 4. 애플리케이션 테스트
애플리케이션을 테스트하려면 JUnit을 사용하여 몇 가지 테스트 사례를 만들어야 한다. SpringJUnit4ClassRunner를 사용하여 테스트 클래스를 시작한다. 컨트롤러에서 선언한 각 주석과 모든 핸들러를 테스트하기 위해 5개의 서로 다른 테스트 사례를 만든다.

`@GetMapping`의 예제 테스트이다.

```java
@Test 
public void giventUrl_whenGetRequest_thenFindGetResponse() throws Exception {

    MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/get");

    ResultMatcher contentMatcher = MockMvcResultMatchers.content().string("GET Response");

    this.mockMvc.perform(builder).andExpect(contentMatcher).andExpect(MockMvcResultMatchers.status().isOk());
}
```

GET URL `/get`에 도달하면 상수 문자열 `GET Response`가 예상된다.

`@PostMapping` 테스트이다.

```java
@Test 
public void givenUrl_whenPostRequest_thenFindPostResponse() throws Exception {

    MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.post("/post");

    ResultMatcher contentMatcher = MockMvcResultMatchers.content().string("POST Response");

    this.mockMvc.perform(builder).andExpect(contentMatcher).andExpect(MockMvcResultMatchers.status().isOk());
}
```

PostMan, RESTClient 등과 같은 일반적인 REST 클라이언트를 항상 사용하여 애플리케이션을 테스트할 수 있다. 이 경우 나머지 클라이언트를 사용하는 동안 올바른 HTTP 메서드 유형을 선택하는 데 약간의 주의가 필요하다. 그렇지 않으면 405 오류 상태가 발생한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-new-requestmapping-shortcuts#2-postmapping](https://www.baeldung.com/spring-new-requestmapping-shortcuts#2-postmapping)
