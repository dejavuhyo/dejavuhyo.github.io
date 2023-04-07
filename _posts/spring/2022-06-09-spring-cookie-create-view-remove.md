---
title: Spring 쿠키 생성, 정보 확인 및 제거
author: dejavuhyo
date: 2022-06-09 16:00:00 +0900
categories: [Framework, Spring]
tags: [spring-cookie, create-cookie, view-cookie-information, remove-cookie, 스프링-쿠키, 쿠키-생성, 쿠키-정보-확인, 쿠키-제거]
---

## 1. 쿠키 생성

```java
public void setCookie(HttpServletResponse response) {
    Cookie cookie = new Cookie("cookieName", "cookieValue");
    cookie.setMaxAge(0); // 쿠키 expiration 타임
    cookie.setPath("/"); // 모든 경로에서 접근 가능 하도록 설정
    response.addCookie(cookie);
}
```

## 2. 쿠키 정보 확인

```java
public void infoCookie(HttpServletRequest request) {
    Cookie[] cookie = request.getCookies();
    for (Cookie cok : cookie) {
        System.out.println("쿠키 이름: " + cok.getName());
        System.out.println("쿠키 값: " + cok.getValue());
    }
}
```

## 3. 쿠키 제거

```java
public void removeCookie(HttpServletResponse response) {
    Cookie cookie = new Cookie("cookieName", null);
    cookie.setMaxAge(0);
    cookie.setPath("/");
    response.addCookie(cookie);
}
```

> 쿠키 정보 확인은 `HttpServletRequest`를 사용하고, 쿠키를 설정은 `HttpServletResponse`를 사용한다.

## [출처 및 참고]
* [https://medium.com/@aaaalpooo/spring-framework-spring-boot-%EC%BF%A0%ED%82%A4-%EC%83%9D%EC%84%B1-%EB%B0%8F-%EC%A0%9C%EA%B1%B0-5aab0bf1ab67](https://medium.com/@aaaalpooo/spring-framework-spring-boot-%EC%BF%A0%ED%82%A4-%EC%83%9D%EC%84%B1-%EB%B0%8F-%EC%A0%9C%EA%B1%B0-5aab0bf1ab67)
