---
title: Spring RequestMapping
author: dejavuhyo
date: 2023-07-14 09:00:00 +0900
categories: [Framework, Spring]
tags: [spring, spring-requestmapping, requestmapping, 스프링, 스프링-요청매핑]
---

## 1. @RequestMapping 기초
몇 가지 기본 기준을 사용하여 HTTP 요청을 메서드에 매핑하는 간단한 예이다. Spring이 기본적으로 루트 컨텍스트 경로 ("/")에서 콘텐츠를 제공한다고 가정한다. 이 문서의 모든 CURL 요청은 기본 루트 컨텍스트 경로에 의존한다.

### 1) @RequestMapping - 경로별

```java
@RequestMapping(value = "/ex/foos", method = RequestMethod.GET)
@ResponseBody
public String getFoosBySimplePath() {
    return "Get some Foos";
}
```

 curl 명령으로 이 매핑을 테스트하려면 다음을 실행한다.

```shell
curl -i http://localhost:8080/ex/foos
```

### 2) @RequestMapping - HTTP 메서드
HTTP 메소드 매개변수에는 기본값이 없다. 따라서 값을 지정하지 않으면 모든 HTTP 요청에 매핑된다.

이전 예제와 유사하지만 HTTP POST 요청에 매핑된 간단한 예제이다.

```java
@RequestMapping(value = "/ex/foos", method = POST)
@ResponseBody
public String postFoos() {
    return "Post some Foos";
}
```

curl 명령을 통해 POST를 테스트하려면 다음을 실행한다.

```shell
curl -i -X POST http://localhost:8080/ex/foos
```

## 2. RequestMapping 및 HTTP 헤더

### 1) @RequestMapping 헤더 속성 사용
요청에 대한 헤더를 지정하여 매핑을 더욱 좁힐 수 있다.

```java
@RequestMapping(value = "/ex/foos", headers = "key=val", method = GET)
@ResponseBody
public String getFoosWithHeader() {
    return "Get some Foos with Header";
}
```

테스트하기 위해 curl 헤더 지원을 사용한다.

```shell
curl -i -H "key:val" http://localhost:8080/ex/foos
```

`@RequestMapping`의 headers 속성을 통해 여러 헤더를 사용할 수도 있다.

```java
@RequestMapping(value = "/ex/foos", headers = { "key1=val1", "key2=val2" }, method = GET)
@ResponseBody
public String getFoosWithHeaders() {
    return "Get some Foos with Header";
}
```

다음 명령으로 테스트할 수 있다.

```shell
curl -i -H "key1:val1" -H "key2:val2" http://localhost:8080/ex/foos
```

curl 구문의 경우 HTTP 사양에서와 같이 콜론으로 헤더 키와 헤더 값을 구분하지만 Spring에서는 등호를 사용한다.

### 2) @RequestMapping 소비 및 생산
컨트롤러 메서드로 생성된 미디어 유형 매핑은 특별한 주의가 필요하다.

`@RequestMapping` 헤더 속성을 통해 Accept 헤더를 기반으로 요청을 매핑할 수 있다.

```java
@RequestMapping(value = "/ex/foos", method = GET, headers = "Accept=application/json")
@ResponseBody
public String getFoosAsJsonFromBrowser() {
    return "Get some Foos with Header Old";
}
```

Accept 헤더를 정의하는 이 방법에 대한 일치는 유연하다. equals 대신에 contains를 사용하므로 다음과 같은 요청은 여전히 ​​올바르게 매핑된다.

```shell
curl -H "Accept:application/json,text/html" http://localhost:8080/ex/foos
```

Spring 3.1부터 `@RequestMapping` 어노테이션은 이러한 목적을 위해 생성 및 소비 속성을 갖는다.

```java
@RequestMapping(value = "/ex/foos", method = RequestMethod.GET, produces = "application/json")
@ResponseBody
public String getFoosAsJsonFromREST() {
    return "Get some Foos with Header New";
}
```

또한 headers 속성을 사용한 이전 유형의 매핑은 Spring 3.1부터 새로운 생성 메커니즘으로 자동 변환되므로 결과는 동일하다.

이것은 같은 방식으로 curl을 통해 소비된다.

```shell
curl -H "Accept:application/json" http://localhost:8080/ex/foos
```

또한 생성은 여러 값도 지원한다.

```java
@RequestMapping(value = "/ex/foos", method = GET, produces = { "application/json", "application/xml" })
```

Accept 헤더를 지정하는 이전 방식과 새로운 방식은 기본적으로 동일한 매핑이므로 Spring은 이를 함께 허용하지 않는다.

이 두 가지 방법을 모두 활성화하면 다음과 같은 결과가 나타난다.

```text
Caused by: java.lang.IllegalStateException: Ambiguous mapping found. 
Cannot map 'fooController' bean method 
java.lang.String 
org.baeldung.spring.web.controller
  .FooController.getFoosAsJsonFromREST()
to 
{ [/ex/foos],
  methods=[GET],params=[],headers=[],
  consumes=[],produces=[application/json],custom=[]
}: 
There is already 'fooController' bean method
java.lang.String 
org.baeldung.spring.web.controller
  .FooController.getFoosAsJsonFromBrowser() 
mapped.
```

대부분의 다른 주석과 다르게 동작하는 새로운 생성 및 소비 메커니즘에 대한 마지막 참고 사항: 유형 수준에서 지정되면 메서드 수준 주석은 보완하지 않고 유형 수준 정보를 재정의한다.

## 3. 경로 변수를 사용한 RequestMapping
매핑 URI의 일부는 `@PathVariable` 주석을 통해 변수에 바인딩될 수 있다.

### 1) 단일 @PathVariable
단일 경로 변수가 있는 간단한 예이다.

```java
@RequestMapping(value = "/ex/foos/{id}", method = GET)
@ResponseBody
public String getFoosBySimplePathWithPathVariable(@PathVariable("id") long id) {
    return "Get a specific Foo with id=" + id;
}
```

curl로 테스트할 수 있다.

```shell
curl http://localhost:8080/ex/foos/1
```

메서드 매개 변수의 이름이 경로 변수의 이름과 정확히 일치하는 경우 값 없이 `@PathVariable`을 사용하여 단순화할 수 있다.

```java
@RequestMapping(value = "/ex/foos/{id}", method = GET)
@ResponseBody
public String getFoosBySimplePathWithPathVariable(@PathVariable String id) {
    return "Get a specific Foo with id=" + id;
}
```

`@PathVariable`은 자동 유형 변환의 이점이 있으므로 id를 다음과 같이 선언할 수도 있다.

```java
@PathVariable long id
```

### 2) 다중 @PathVariable
더 복잡한 URI는 URI의 여러 부분을 여러 값 에 매핑해야 할 수 있다.

```java
@RequestMapping(value = "/ex/foos/{fooid}/bar/{barid}", method = GET)
@ResponseBody
public String getFoosBySimplePathWithPathVariables(@PathVariable long fooid, @PathVariable long barid) {
    return "Get a specific Bar with id=" + barid + " from a Foo with id=" + fooid;
}
```

이것은 같은 방식으로 컬을 사용하여 쉽게 테스트할 수 있다.

```shell
curl http://localhost:8080/ex/foos/1/bar/2
```

### 3) 정규식을 사용한 @PathVariable
`@PathVariable`을 매핑할 때 정규식을 사용할 수도 있다.

예를 들어 id에 대한 숫자 값만 허용하도록 매핑을 제한한다.

```java
@RequestMapping(value = "/ex/bars/{numericId:[\\d]+}", method = GET)
@ResponseBody
public String getBarsBySimplePathWithPathVariable(@PathVariable long numericId) {
    return "Get a specific Bar with id=" + numericId;
}
```

이는 다음 URI가 일치함을 의미한다.

```text
http://localhost:8080/ex/bars/1
```

그러나 이것은 다음을 수행하지 않는다.

```java
http://localhost:8080/ex/bars/abc
```

## 4. 요청 매개변수를 사용한 요청 매핑
`@RequestMapping`을 사용하면 `@RequestParam` 주석을 사용하여 URL 매개변수를 쉽게 매핑할 수 있다.

요청을 URI에 매핑한다.

```text
http://localhost:8080/ex/bars?id=100
```

```java
@RequestMapping(value = "/ex/bars", method = GET)
@ResponseBody
public String getBarBySimplePathWithRequestParam(@RequestParam("id") long id) {
    return "Get a specific Bar with id=" + id;
}
```

그런 다음 컨트롤러 메서드 서명에서 `@RequestParam("id")` 주석을 사용하여 id 매개 변수의 값을 추출한다.

id 매개변수로 요청을 보내려면 curl에서 매개변수 지원을 사용한다.

```shell
curl -i -d id=100 http://localhost:8080/ex/bars
```

이 예에서 매개변수는 먼저 선언되지 않고 직접 바인딩되었다.

고급 시나리오의 경우 `@RequestMapping`은 요청 매핑을 좁히는 또 다른 방법으로 매개 변수를 선택적으로 정의할 수 있다.

```java
@RequestMapping(value = "/ex/bars", params = "id", method = GET)
@ResponseBody
public String getBarBySimplePathWithExplicitRequestParam(@RequestParam("id") long id) {
    return "Get a specific Bar with id=" + id;
}
```

훨씬 더 유연한 매핑이 허용된다. 여러 매개변수 값을 설정할 수 있으며 모두 사용할 필요는 없다.

```java
@RequestMapping(value = "/ex/bars", params = { "id", "second" }, method = GET)
@ResponseBody
public String getBarBySimplePathWithExplicitRequestParams(@RequestParam("id") long id) {
    return "Narrow Get a specific Bar with id=" + id;
}
```

물론 다음과 같은 URI에 대한 요청도 있다.

```text
http://localhost:8080/ex/bars?id=100&second=something
```

항상 가장 일치하는 항목에 매핑된다. 이는 id와 두 번째 매개변수를 모두 정의하는 더 좁은 일치 항목이다.

## 5. RequestMapping 코너 케이스

### 1) @RequestMapping - 동일한 컨트롤러 메서드에 매핑된 여러 경로
일반적 으로 단일 `@RequestMapping` 경로 값이 단일 컨트롤러 메서드에 사용되지만(엄격하고 빠른 규칙이 아닌 모범 사례일 뿐임) 여러 요청을 동일한 메서드에 매핑해야 하는 경우가 있다.

이 경우 `@RequestMapping`의 값 속성은 단일 매핑이 아닌 여러 매핑을 허용한다.

```java
@RequestMapping(value = { "/ex/advanced/bars", "/ex/advanced/foos" }, method = GET)
@ResponseBody
public String getFoosOrBarsByPath() {
    return "Advanced - Get some Foos or Bars";
}
```

이제 이 두 컬 명령은 모두 동일한 메서드에 도달해야 한다.

```shell
curl -i http://localhost:8080/ex/advanced/foos
curl -i http://localhost:8080/ex/advanced/bars
```

### 2) @RequestMapping - 동일한 컨트롤러 메서드에 대한 여러 HTTP 요청 메서드
서로 다른 HTTP 동사를 사용하는 여러 요청을 동일한 컨트롤러 메서드에 매핑할 수 있다.

```java
@RequestMapping(value = "/ex/foos/multiple", method = { RequestMethod.PUT, RequestMethod.POST })
@ResponseBody
public String putAndPostFoos() {
    return "Advanced - PUT and POST within single method";
}
```

curl을 사용하면 이제 두 가지 모두 동일한 방법을 사용한다.

```shell
curl -i -X POST http://localhost:8080/ex/foos/multiple
curl -i -X PUT http://localhost:8080/ex/foos/multiple
```

### 3) @RequestMapping - 모든 요청에 ​​대한 폴백
GET에 대해 특정 HTTP 메서드를 사용하는 모든 요청에 ​​대해 간단한 폴백을 구현하려면 다음을 수행한다.

```java
@RequestMapping(value = "*", method = RequestMethod.GET)
@ResponseBody
public String getFallback() {
    return "Fallback for GET Requests";
}
```

모든 요청에 ​​대해서도 다음을 수행한다.

```java
@RequestMapping(value = "*", method = { RequestMethod.GET, RequestMethod.POST ... })
@ResponseBody
public String allFallback() {
    return "Fallback for All Requests";
}
```

### 4) 모호한 매핑 오류
모호한 매핑 오류는 Spring이 서로 다른 컨트롤러 메서드에 대해 둘 이상의 요청 매핑을 동일하게 평가할 때 발생한다. HTTP 메서드, URL, 매개변수, 헤더 및 미디어 유형이 동일한 요청 매핑은 동일하다.

예를 들어 다음은 모호한 매핑이다.

```java
@GetMapping(value = "foos/duplicate" )
public String duplicate() {
    return "Duplicate";
}

@GetMapping(value = "foos/duplicate" )
public String duplicateEx() {
    return "Duplicate";
}
```

throw된 예외에는 일반적으로 다음과 같은 오류 메시지가 있다.

```text
Caused by: java.lang.IllegalStateException: Ambiguous mapping.
  Cannot map 'fooMappingExamplesController' method 
  public java.lang.String org.baeldung.web.controller.FooMappingExamplesController.duplicateEx()
  to {[/ex/foos/duplicate],methods=[GET]}:
  There is already 'fooMappingExamplesController' bean method
  public java.lang.String org.baeldung.web.controller.FooMappingExamplesController.duplicate() mapped.
```

오류 메시지를 주의 깊게 읽으면 Spring이 이미 매핑된 `org.baeldung.web.controller.FooMappingExamplesController.duplicate()`와 충돌하는 매핑이 있으므로 `org.baeldung.web.controller.FooMappingExamplesController.duplicateEx()` 메서드를 매핑할 수 없다는 사실을 지적한다.

아래 코드는 두 메서드가 서로 다른 콘텐츠 유형을 반환하기 때문에 모호한 매핑 오류가 발생하지 않는다.

```java
@GetMapping(value = "foos/duplicate", produces = MediaType.APPLICATION_XML_VALUE)
public String duplicateXml() {
    return "<message>Duplicate</message>";
}

@GetMapping(value = "foos/duplicate", produces = MediaType.APPLICATION_JSON_VALUE)
public String duplicateJson() {
    return "{\"message\":\"Duplicate\"}";
}
```

이러한 차별화를 통해 컨트롤러는 요청에 제공된 Accepts 헤더를 기반으로 올바른 데이터 표현을 반환할 수 있다.

이 문제를 해결하는 또 다른 방법은 관련된 두 가지 방법 중 하나에 할당된 URL을 업데이트하는 것이다.

## 6. 새로운 요청 매핑 바로 가기
Spring Framework 4.3은 모두 `@RequestMapping`에 기반한 몇 가지 새로운 HTTP 매핑 주석을 도입했다.

* @GetMapping

* @PostMapping

* @PutMapping

* @DeleteMapping

* @PatchMapping

이러한 새로운 주석은 가독성을 향상시키고 코드의 장황함을 줄일 수 있다.

CRUD 작업을 지원하는 RESTful API를 생성하여 작동 중인 이러한 새 주석을 확인한다.

```java
@GetMapping("/{id}")
public ResponseEntity<?> getBazz(@PathVariable String id){
    return new ResponseEntity<>(new Bazz(id, "Bazz"+id), HttpStatus.OK);
}

@PostMapping
public ResponseEntity<?> newBazz(@RequestParam("name") String name){
    return new ResponseEntity<>(new Bazz("5", name), HttpStatus.OK);
}

@PutMapping("/{id}")
public ResponseEntity<?> updateBazz(
  @PathVariable String id,
  @RequestParam("name") String name) {
    return new ResponseEntity<>(new Bazz(id, name), HttpStatus.OK);
}

@DeleteMapping("/{id}")
public ResponseEntity<?> deleteBazz(@PathVariable String id){
    return new ResponseEntity<>(new Bazz(id), HttpStatus.OK);
}
```

 자세한 내용은 [여기](https://www.baeldung.com/spring-new-requestmapping-shortcuts)에서 확인할 수 있다.

## 7. 스프링 구성
FooController가 다음 패키지에 정의되어 있다는 점을 고려하면 Spring MVC 구성은 충분히 간단하다.

```java
package org.baeldung.spring.web.controller;

@Controller
public class FooController { ... }
```

전체 MVC 지원을 활성화하고 컨트롤러에 대한 클래스 경로 검색을 구성하려면 `@Configuration` 클래스가 필요하다.

```java
@Configuration
@EnableWebMvc
@ComponentScan({ "org.baeldung.spring.web.controller" })
public class MvcConfig {
    //
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-requestmapping](https://www.baeldung.com/spring-requestmapping)
