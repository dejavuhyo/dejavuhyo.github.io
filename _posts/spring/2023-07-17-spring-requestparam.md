---
title: Spring RequestParam
author: dejavuhyo
date: 2023-07-17 11:30:00 +0900
categories: [Framework, Spring]
tags: [spring-requestparam, requestparam, 스프링-requestparam]
---

## 1. 간단한 매핑
id라는 쿼리 매개변수를 사용하는 엔드포인트 `/api/foos`가 있다.

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam String id) {
    return "ID: " + id;
}
```

이 예에서는 `@RequestParam`을 사용하여 id 쿼리 매개변수를 추출했다.

간단한 GET 요청은 getFoos를 호출한다.

```text
http://localhost:8080/spring-mvc-basics/api/foos?id=abc
----
ID: abc
```

## 2. 요청 매개변수 이름 지정
이전 예에서 변수 이름과 매개변수 이름이 모두 동일하다.

Spring Boot를 사용하지 않는 경우 특별한 컴파일 타임 구성을 수행해야 하거나 매개변수 이름이 실제로 바이트코드에 포함되지 않을 수 있다.

다행스럽게도 name 속성을 사용하여 `@RequestParam` 이름을 구성할 수 있다.

```java
@PostMapping("/api/foos")
@ResponseBody
public String addFoo(@RequestParam(name = "id") String fooId, @RequestParam String name) {
    return "ID: " + fooId + " Name: " + name;
}
```

`@RequestParam(value = "id")` 또는 `@RequestParam("id")`만 수행할 수도 있다.

## 3. 선택적 요청 매개변수
`@RequestParam` 주석이 달린 메서드 매개변수는 기본적으로 필요하다.

즉, 매개변수가 요청에 없으면 오류가 발생한다.

```text
GET /api/foos HTTP/1.1
-----
400 Bad Request
Required String parameter 'id' is not present
```

필수 속성을 사용하여 `@RequestParam`을 선택 사항으로 구성할 수 있다.

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam(required = false) String id) {
    return "ID: " + id;
}
```

이 경우 아래와 같다.

```text
http://localhost:8080/spring-mvc-basics/api/foos?id=abc
----
ID: abc
```

```text
http://localhost:8080/spring-mvc-basics/api/foos
----
ID: null
```

메서드를 올바르게 호출한다.

매개 변수가 지정되지 않은 경우 메서드 매개 변수는 null 에 바인딩된다.

### 1) Java 8 사용 선택 사항
옵션으로 매개변수를 래핑할 수 있다.

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam Optional<String> id){
    return "ID: " + id.orElseGet(() -> "not provided");
}
```

이 경우 필수 속성을 지정할 필요가 없다.

요청 매개변수가 제공되지 않으면 기본값이 사용된다.

```text
http://localhost:8080/spring-mvc-basics/api/foos 
---- 
ID: not provided
```

## 4. 요청 매개변수의 기본값
defaultValue 속성을 사용하여 `@RequestParam`에 기본값을 설정할 수도 있다.

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam(defaultValue = "test") String id) {
    return "ID: " + id;
}
```

이는 사용자가 더 이상 매개변수를 제공할 필요가 없다는 점에서 `required=false`와 같다.

```text
http://localhost:8080/spring-mvc-basics/api/foos
----
ID: test
```

다음과 같이 제공해도 된다.

```text
http://localhost:8080/spring-mvc-basics/api/foos?id=abc
----
ID: abc
```

defaultValue 속성을 설정할 때 required는 실제로 false로 설정된다.

## 5. 모든 매개변수 매핑
Map을 사용하여 이름이나 개수를 정의하지 않고 여러 매개변수를 가질 수도 있다.

```java
@PostMapping("/api/foos")
@ResponseBody
public String updateFoos(@RequestParam Map<String,String> allParams) {
    return "Parameters are " + allParams.entrySet();
}
```

그런 다음 전송된 매개변수를 다시 반영한다.

```text
curl -X POST -F 'name=abc' -F 'id=123' http://localhost:8080/spring-mvc-basics/api/foos
-----
Parameters are {[name=abc], [id=123]}
```

## 6. 다중 값 매개변수 매핑
단일 `@RequestParam`은 여러 값을 가질 수 있다.

```java
@GetMapping("/api/foos")
@ResponseBody
public String getFoos(@RequestParam List<String> id) {
    return "IDs are " + id;
}
```

그리고 Spring MVC는 쉼표로 구분된 id 매개변수를 매핑한다.

```text
http://localhost:8080/spring-mvc-basics/api/foos?id=1,2,3
----
IDs are [1,2,3]
```

또는 별도의 id 매개변수 목록이다.

```text
http://localhost:8080/spring-mvc-basics/api/foos?id=1&id=2
----
IDs are [1,2]
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-request-param](https://www.baeldung.com/spring-request-param)
