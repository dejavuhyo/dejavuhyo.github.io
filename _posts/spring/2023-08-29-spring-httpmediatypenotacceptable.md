---
title: Spring HttpMediaTypeNotAcceptableException
author: dejavuhyo
date: 2023-08-29 10:00:00 +0900
categories: [Framework, Spring]
tags: [spring-exception, 스프링-예외]
---

## 1. 문제
Spring으로 API 엔드포인트를 구현할 때 일반적으로 소비/생산 미디어 유형을 지정해야 한다(소비 및 생성 매개변수를 통해). 이렇게 하면 API가 해당 특정 작업에 대해 클라이언트에 다시 반환할 수 있는 형식이 좁아진다.

HTTP 에는 클라이언트가 인식하고 수락할 수 있는 미디어 유형을 지정하는 데 사용되는 전용 "Accept" 헤더도 있다. 간단히 말해서, 서버는 클라이언트가 요청한 미디어 유형 중 하나를 사용하여 리소스 표현을 다시 보낸다.

그러나 양측이 작업할 수 있는 공통 유형이 없으면 Spring은 HttpMediaTypeNotAcceptableException 예외를 발생시킨다.

## 2. 실제 사례
간단한 예제이다.

`application/json`에서만 작동 하고 JSON 데이터도 반환하는 POST 엔드포인트를 사용한다.

```java
@PostMapping(
  value = "/test", 
  consumes = MediaType.APPLICATION_JSON_VALUE, 
  produces = MediaType.APPLICATION_JSON_VALUE)
public Map<String, String> example() {
    return Collections.singletonMap("key", "value");
}
```

인식할 수 없는 콘텐츠 유형이 포함된 CURL을 사용하여 요청을 보낸다.

```shell
curl -X POST --header "Accept: application/pdf" http://localhost:8080/test -v

> POST /test HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.51.0
> Accept: application/pdf
```

응답은 다음과 같다.

```text
< HTTP/1.1 406 
< Content-Length: 0
```

## 3. 해결책
문제를 해결하는 방법은 지원되는 유형 중 하나를 보내고 받는 것이다.

허용 가능한 모든 미디어 유형에 대해 클라이언트에 알리는 사용자 정의 ExceptionHandler를 사용하여 보다 설명적인 메시지(기본적으로 Spring은 빈 본문을 반환함)를 제공하는 것이다.

`application/json` 뿐이다.

```java
@ResponseBody
@ExceptionHandler(HttpMediaTypeNotAcceptableException.class)
public String handleHttpMediaTypeNotAcceptableException() {
    return "acceptable MIME type:" + MediaType.APPLICATION_JSON_VALUE;
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-httpmediatypenotacceptable](https://www.baeldung.com/spring-httpmediatypenotacceptable)
