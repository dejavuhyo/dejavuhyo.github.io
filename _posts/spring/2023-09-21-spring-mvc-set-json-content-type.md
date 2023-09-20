---
title: Spring MVC JSON 콘텐츠 유형 설정 방법
author: dejavuhyo
date: 2023-09-21 08:00:00 +0900
categories: [Framework, Spring]
tags: [spring-json, json-content-type, content-type, 스프링-콘텐츠-유형, 콘텐츠-유형]
---

## 1. 스프링 @RequestMapping
`@RequestMapping`은 웹 요청을 Spring 컨트롤러에 매핑하는 중요한 주석이다. HTTP 메소드, 요청 매개변수, 헤더 및 미디어 유형을 포함한 다양한 속성을 가지고 있다.

일반적으로 미디어 유형은 소비 가능 미디어와 생산 가능 미디어의 두 가지 범주로 분류된다. 이 외에도 Spring에서 사용자 정의 미디어 유형을 정의할 수도 있다. **주요 목적은 요청 핸들러의 미디어 유형 목록에 대한 기본 매핑을 제한하는 것이다.**

### 1) 소모성 미디어 유형
Consumes 속성을 사용하면 컨트롤러가 클라이언트에서 수락할 미디어 유형을 지정할 수 있다. 미디어 유형 목록도 제공할 수 있다. 간단한 엔드포인트를 정의한다.

```java
@RequestMapping(value = "/greetings", method = RequestMethod.POST, consumes="application/json")
public void addGreeting(@RequestBody ContentType type, Model model) {
    // code here
}
```

클라이언트가 리소스별로 사용할 수 없는 미디어 유형을 지정하는 경우 시스템은 HTTP "415 Unsupported Media Type" 오류를 생성한다.

### 2) 생산 가능한 미디어 유형
Consumes 속성과 달리 생산은 리소스가 생성하여 클라이언트에 다시 보낼 수 있는 미디어 유형을 지정한다. 의심할 바 없이 옵션 목록을 사용할 수 있다. 리소스가 요청된 리소스를 생성할 수 없는 경우 시스템은 HTTP "406 Not Acceptable" 오류를 생성한다.

JSON 문자열을 노출하는 API의 간단한 예이다.

엔드포인트는 다음과 같다.

```java
@GetMapping(
  value = "/greetings-with-response-body", 
  produces="application/json"
) 
public String getGreetingWhileReturnTypeIsString() { 
    return "{\"test\": \"Hello\"}";
}
```

CURL을 사용하여 이를 테스트한다.

```shell
curl http://localhost:8080/greetings-with-response-body
```

위 명령은 다음과 같은 응답을 생성한다.

```json
{ "test": "Hello" }
```

## 2. 스프링 부트 Rest Controller
나머지 컨트롤러에 Spring Boot를 사용하는 경우 여러 작업을 처리하는 단일 주석을 통해 생활이 쉬워진다. `@RestController` 주석은 `@Controller` 주석과 `@ResponseBody` 주석을 하나로 결합한다. 이는 해당 클래스에 정의된 모든 엔드포인트에 적용된다.

### 1) @RestController 주석 사용
Jackson ObjectMapper 클래스는 문자열, 스트림 또는 파일에서 JSON을 구문 분석한다. Jackson이 클래스 경로에 있는 경우 Spring 애플리케이션의 모든 컨트롤러는 기본적으로 JSON 응답을 렌더링한다.

응답을 확인하기 위해 단위 테스트를 추가한다.

```java
@Test
public void givenReturnTypeIsString_whenJacksonOnClasspath_thenDefaultContentTypeIsJSON() 
  throws Exception {
    
    // Given
    String expectedMimeType = "application/json";
    
    // Then
    String actualMimeType = this.mockMvc.perform(MockMvcRequestBuilders.get("/greetings-with-response-body", 1))
      .andReturn().getResponse().getContentType();

    Assert.assertEquals(expectedMimeType, actualMimeType);
}
```

### 2) ResponseEntity 사용
`@ResponseBody`와 달리 ResponseEntity는 전체 HTTP 응답을 나타내는 일반 유형이다. 결과적으로 상태 코드, 헤더, 본문 등 여기에 들어가는 모든 것을 제어할 수 있다.

새로운 엔드포인트를 정의한다.

```java
@GetMapping(
  value = "/greetings-with-response-entity",
  produces = "application/json"
)
public ResponseEntity<String> getGreetingWithResponseEntity() {
    final HttpHeaders httpHeaders= new HttpHeaders();
    httpHeaders.setContentType(MediaType.APPLICATION_JSON);
    return new ResponseEntity<String>("{\"test\": \"Hello with ResponseEntity\"}", httpHeaders, HttpStatus.OK);
}
```

브라우저의 개발자 콘솔에서 다음 응답을 볼 수 있다.

```json
{"test": "Hello with ResponseEntity"}
```

테스트를 통해 응답의 콘텐츠 유형을 확인한다.

```java
@Test
public void givenReturnTypeIsResponseEntity_thenDefaultContentTypeIsJSON() throws Exception {
    
    // Given
    String expectedMimeType = "application/json";
    
    // Then
    String actualMimeType = this.mockMvc.perform(MockMvcRequestBuilders.get("/greetings-with-response-entity", 1))
      .andReturn().getResponse().getContentType();

    Assert.assertEquals(expectedMimeType, actualMimeType);
}
```

### 3) Map<String, Object> 반환 유형 사용
반환 유형을 String에서 Map으로 변경하여 콘텐츠 유형을 설정할 수도 있다. 이 Map 반환 유형은 marshaling이 필요하며 JSON 개체를 반환한다.

새로운 엔드포인트는 다음과 같다.

```java
@GetMapping(
  value = "/greetings-with-map-return-type", 
  produces = "application/json"
)
public Map<String, Object> getGreetingWhileReturnTypeIsMap() {
    HashMap<String, Object> map = new HashMap<String, Object>();
    map.put("test", "Hello from map");
    return map;
}
```

이를 확인한다.

```shell
curl http://localhost:8080/greetings-with-map-return-type
```

JSON 응답을 반환한다.

```json
{ "test": "Hello from map" }
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-set-json-content-type#rest-controllers-with-spring-boot](https://www.baeldung.com/spring-mvc-set-json-content-type#rest-controllers-with-spring-boot)
