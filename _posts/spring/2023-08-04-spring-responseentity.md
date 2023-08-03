---
title: Spring ResponseEntity
author: dejavuhyo
date: 2023-08-04 08:20:00 +0900
categories: [Framework, Spring]
tags: [spring-responseentity, responseentity, http-response]
---

## 1. ResponseEntity
ResponseEntity는 전체 HTTP 응답(상태 코드, 헤더 및 본문)을 나타낸다. 결과적으로 이를 사용하여 HTTP 응답을 완전히 구성할 수 있다.

사용하려면 엔드포인트에서 반환해야 한다. 스프링이 나머지를 처리한다.

ResponseEntity는 일반 유형이다. 결과적으로 응답 본문으로 모든 유형을 사용할 수 있다.

```java
@GetMapping("/hello")
ResponseEntity<String> hello() {
    return new ResponseEntity<>("Hello World!", HttpStatus.OK);
}
```

프로그래밍 방식으로 응답 상태를 지정하므로 시나리오마다 다른 상태 코드로 반환할 수 있다.

```java
@GetMapping("/age")
ResponseEntity<String> age(
  @RequestParam("yearOfBirth") int yearOfBirth) {

    if (isInFuture(yearOfBirth)) {
        return new ResponseEntity<>(
          "Year of birth cannot be in the future", 
          HttpStatus.BAD_REQUEST);
    }

    return new ResponseEntity<>("Your age is " + calculateAge(yearOfBirth), HttpStatus.OK);
}
```

또한 HTTP 헤더를 설정할 수 있다.

```java
@GetMapping("/customHeader")
ResponseEntity<String> customHeader() {
    HttpHeaders headers = new HttpHeaders();
    headers.add("Custom-Header", "foo");

    return new ResponseEntity<>("Custom header set", headers, HttpStatus.OK);
}
```

또한 ResponseEntity는 HeadersBuilder 및 하위 인터페이스인 BodyBuilder 라는 두 개의 중첩된 빌더 인터페이스를 제공한다. 따라서 ResponseEntity의 정적 메서드를 통해 해당 기능에 액세스할 수 있다.

가장 간단한 경우는 본문과 HTTP 200 응답 코드가 있는 응답이다.

```java
@GetMapping("/hello")
ResponseEntity<String> hello() {
    return ResponseEntity.ok("Hello World!");
}
```

가장 많이 사용되는 [HTTP 상태 코드](https://www.baeldung.com/cs/http-status-codes)의 경우 정적 메서드를 얻는다.

```java
BodyBuilder accepted();
BodyBuilder badRequest();
BodyBuilder created(java.net.URI location);
HeadersBuilder<?> noContent();
HeadersBuilder<?> notFound();
BodyBuilder ok();
```

또한 BodyBuilder status(HttpStatus status) 및 BodyBuilder status(int status) 메서드를 사용하여 HTTP 상태를 설정할 수 있다.

마지막으로 `ResponseEntity<T> BodyBuilder.body(T body)`를 사용하여 HTTP 응답 본문을 설정할 수 있다.

```java
@GetMapping("/age")
ResponseEntity<String> age(@RequestParam("yearOfBirth") int yearOfBirth) {
    if (isInFuture(yearOfBirth)) {
        return ResponseEntity.badRequest()
            .body("Year of birth cannot be in the future");
    }

    return ResponseEntity.status(HttpStatus.OK).body("Your age is " + calculateAge(yearOfBirth));
}
```

사용자 지정 헤더를 설정할 수도 있다.

```java
@GetMapping("/customHeader")
ResponseEntity<String> customHeader() {
    return ResponseEntity.ok()
        .header("Custom-Header", "foo")
        .body("Custom header set");
}
```

`BodyBuilder.body()`는 BodyBuilder 대신 ResponseEntity를 반환하므로 마지막 호출이어야 한다.

HeaderBuilder를 사용하면 응답 본문의 속성을 설정할 수 없다.

컨트롤러에서 `ResponseEntity<T>` 개체를 반환하는 동안 요청을 처리하는 동안 예외 또는 오류가 발생할 수 있으며 오류 관련 정보를 다른 유형(예: E)으로 표시되는 사용자에게 반환하려고 한다.

Spring 3.2는 이러한 종류의 시나리오를 처리하는 새로운 `@ControllerAdvice` 주석과 함께 전역 `@ExceptionHandler`에 대한 [지원을 제공](https://www.baeldung.com/exception-handling-for-rest-with-spring)한다.

ResponseEntity는 매우 강력 하지만 남용해서는 안 된다. 간단한 경우에는 요구를 충족하는 다른 옵션이 있으며 훨씬 더 깨끗한 코드를 생성한다.

## 2. 대안

### 1) @ResponseBody
고전적인 Spring MVC 애플리케이션에서 엔드포인트는 일반적으로 렌더링된 HTML 페이지를 반환한다. 때로는 실제 데이터만 반환하면 된다. 예를 들어 AJAX와 함께 엔드포인트를 사용할 때이다.

이러한 경우 `@ResponseBody`로 요청 처리기 메서드를 표시할 수 있으며 Spring은 메서드의 결과 값을 HTTP 응답 본문 자체로 처리한다.

### 2) @ResponseStatus
엔드포인트가 성공적으로 반환되면 Spring은 HTTP 200(OK) 응답을 제공한다. 엔드포인트가 예외를 발생시키면 Spring은 사용할 HTTP 상태를 알려주는 예외 핸들러를 찾는다.

`@ResponseStatus`로 이러한 메서드를 표시할 수 있으므로 Spring은 사용자 지정 [HTTP 상태로 반환](https://www.baeldung.com/spring-response-status)된다.

### 3) 응답을 직접 조작
Spring은 또한 `javax.servlet.http.HttpServletResponse` 객체에 직접 접근할 수 있게 해준다. 메서드 인수로 선언하기만 하면 된다.

```java
@GetMapping("/manual")
void manual(HttpServletResponse response) throws IOException {
    response.setHeader("Custom-Header", "foo");
    response.setStatus(200);
    response.getWriter().println("Hello World!");
}
```

Spring은 기본 구현 위에 추상화 및 추가 기능을 제공하므로 이러한 방식으로 응답을 조작해서는 안된다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-response-entity](https://www.baeldung.com/spring-response-entity)
