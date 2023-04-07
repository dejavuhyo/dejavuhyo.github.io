---
title: Spring Get, Post 동시에 받는 방법
author: dejavuhyo
date: 2023-04-05 10:05:00 +0900
categories: [Framework, Spring]
tags: [spring-get-post, get-post, get-post-same-time, 스프링-겟-포스트, 겟-포스트, 겟-포스트-동시]
---

## 1. RequestMapping
유연한 메서드 서명을 사용하여 요청 처리 클래스의 메서드에 웹 요청을 매핑하기 위한 주석이다.

이 주석은 클래스와 메서드 수준에서 모두 사용할 수 있다. 대부분의 경우 메서드 수준에서 응용 프로그램은 HTTP 메서드 특정 변형 `@GetMapping`, 또는 `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping` 중 하나를 사용하는 것을 선호한다.

## 2. 방법

```java
@RequestMapping(value="/test", method={RequestMethod.GET, RequestMethod.POST})
```

## [출처 및 참고]
* [https://okky.kr/questions/1054132](https://okky.kr/questions/1054132)
* [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html)
