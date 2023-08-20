---
title: Spring Controllers Returning Custom Status Codes
author: dejavuhyo
date: 2023-08-21 07:35:00 +0900
categories: [Framework, Spring]
tags: [spring-custom-code, http-status-code, http-code]
---

## 1. 사용자 지정 상태 코드 반환
Spring은 컨트롤러 클래스에서 사용자 정의 상태 코드를 반환하는 몇 가지 기본 방법을 제공한다.

* ResponseEntity 사용

* 예외 클래스에서 `@ResponseStatus` 주석 사용

* `@ControllerAdvice` 및 `@ExceptionHandler` 주석 사용

이러한 옵션은 상호 배타적이지 않고 서로를 보완할 수 있다.

상세한 내용은 [여기](https://www.baeldung.com/exception-handling-for-rest-with-spring#controlleradvice)를 참고한다.

### 1) ResponseEntity를 통해 상태 코드 반환
표준 Spring MVC 컨트롤러에서 간단한 매핑을 정의한다.

```java
@RequestMapping(value = "/controller", method = RequestMethod.GET)
@ResponseBody
public ResponseEntity sendViaResponseEntity() {
    return new ResponseEntity(HttpStatus.NOT_ACCEPTABLE);
}
```

`/controller`에 대한 GET 요청을 수신하면 Spring은 406 코드(허용되지 않음)로 응답을 반환한다. 이 예제에 대한 특정 응답 코드를 임의로 선택했다. 모든 HTTP 상태 코드를 반환할 수 있다(전체 목록은 여기에서 찾을 수 있음).

### 2)  예외를 통해 상태 코드 반환
Exception을 사용하여 상태 코드를 반환하는 방법을 보여주기 위해 컨트롤러에 두 번째 메서드를 추가한다.

```java
@RequestMapping(value = "/exception", method = RequestMethod.GET)
@ResponseBody
public ResponseEntity sendViaException() {
    throw new ForbiddenException();
}
```

`/exception`에 대한 GET 요청을 수신하면 Spring은 ForbiddenException을 발생킨다. 이것은 별도의 클래스에서 정의할 사용자 지정 예외이다.

```java
@ResponseStatus(HttpStatus.FORBIDDEN)
public class ForbiddenException extends RuntimeException {}
```

이 예외에는 코드가 필요하지 않다. 모든 작업은 `@ResponseStatus` 주석에 의해 수행된다.

이 경우 예외가 발생하면 예외를 발생시킨 컨트롤러는 응답 코드 403(Forbidden)과 함께 응답을 반환한다. 필요한 경우 응답과 함께 반환될 메시지를 주석에 추가할 수도 있다.

이 경우 클래스는 다음과 같다.

```java
@ResponseStatus(value = HttpStatus.FORBIDDEN, reason="To show an example of a custom message")
public class ForbiddenException extends RuntimeException {}
```

예외가 상태 코드를 반환하도록 하는 것이 기술적으로 가능하지만 대부분의 경우 오류 코드(4XX 및 5XX)에 대한 예외를 사용하는 것이 논리적으로 타당하다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-controller-custom-http-status-code](https://www.baeldung.com/spring-mvc-controller-custom-http-status-code)
