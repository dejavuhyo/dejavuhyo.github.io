---
title: Spring ResponseStatus를 사용하여 HTTP 상태 코드 설정
author: dejavuhyo
date: 2023-07-21 08:50:00 +0900
categories: [Framework, Spring]
tags: [spring-responsestatus, responsestatus, http-status-code]
---

## 1. 컨트롤러 메서드
엔드포인트가 성공적으로 반환되면 Spring은 HTTP 200(OK) 응답을 제공한다.

컨트롤러 메서드의 응답 상태를 지정하려면 해당 메서드를 `@ResponseStatus`로 표시할 수 있다. 원하는 응답 상태에 대한 두 개의 교환 가능한 인수인 코드와 값이 있다.

```java
@ResponseStatus(HttpStatus.I_AM_A_TEAPOT)
void teaPot() {}
```

오류를 알리고 싶을 때 reason argument를 통해 오류 메시지를 제공할 수 있다.

```java
@ResponseStatus(HttpStatus.BAD_REQUEST, reason = "Some parameters are invalid")
void onIllegalArgumentException(IllegalArgumentException exception) {}
```

reason을 설정할 때 Spring은 `HttpServletResponse.sendError()`를 호출한다. 따라서 클라이언트에 HTML 오류 페이지를 보내므로 REST endpoints에 적합하지 않다.

또한 Spring은 표시된 메소드가 성공적으로 완료될 때 (Exception을 던지지 않고) `@ResponseStatus`만 사용한다.

## 2. 오류 처리기 사용
`@ResponseStatus`를 사용하여 예외를 HTTP 응답 상태로 변환하는 세 가지 방법이 있다.

* @ExceptionHandler 사용

* @ControllerAdvice 사용

* Exception 클래스 표시

처음 두 방법을 사용하려면 오류 처리기 메서드를 정의해야 한다. [여기](https://www.baeldung.com/exception-handling-for-rest-with-spring)에 이 항목에 대한 자세한 내용이 있다.

일반 MVC 메서드와 동일한 방식으로 이러한 오류 처리기 메서드와 함께 `@ResponseStatus`를 사용할 수 있다.

동적 오류 응답이 필요하지 않은 경우 가장 간단한 솔루션은 세 번째 방법이다. `@ResponseStatus`로 Exception 클래스를 표시한다.

```java
@ResponseStatus(code = HttpStatus.BAD_REQUEST)
class CustomException extends RuntimeException {}
```

Spring이 이 Exception을 포착하면 `@ResponseStatus`에서 제공한 설정을 사용한다.

Exception 클래스를 `@ResponseStatus`로 표시하면 Spring은 이유 설정 여부에 관계 없이 항상 `HttpServletResponse.sendError()`를 호출 한다.

또한 `@ResponseStatus`로 표시하지 않는 한 Spring은 하위 클래스에 대해 동일한 구성을 사용한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-response-status](https://www.baeldung.com/spring-response-status)
