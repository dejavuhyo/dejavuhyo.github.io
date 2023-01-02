---
title: Spring Boot 세션 쿠키 이름 변경
author: dejavuhyo
date: 2022-06-08 15:40:00 +0900
categories: [Application, Framework]
tags: [rename-session-cookies, rename-session, session-cookies, rename-cookies, 세션-쿠키-이름-변경, 세션-이름-변경, 쿠키-이름-변경, 세션-쿠키, 세션, 쿠키]
---

## 1. 세션 쿠키 이름 변경
Spring 세션을 사용하면 Tomcat 서버의 기본 JSESSIONID 쿠키는 스프링에서 디폴트로 지정해주는 "SESSION"이라는 이름의 쿠키로 변경된다.

properties 파일에서 설정할 수 있다.

```properties
server.servlet.session.cookie.name=CHANGESESSION
```

## [출처 및 참고]
* [https://brunch.co.kr/@springboot/114](https://brunch.co.kr/@springboot/114)
