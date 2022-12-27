---
title: Spring Boot 하나의 yml 파일로 개발 및 운영 분리
author: dejavuhyo
date: 2022-04-26 11:10:00 +0900
categories: [Application, Framework]
tags: [spring-boot-yml, yml-separate, yml-분리, yml-개발-운영]
---

## 1. activate 설정
application.yml에 active하려는 환경을 설정한다.

```yml
spring:
  profiles:
    active: local
```

## 2. 내용 작성
application.yml에 local, dev, prod를 설정한다.

```yml
spring:
  profiles:
    active: local

---
# local
spring:
  config:
    activate:
      on-profile: local
  datasource:
    driver-class-name: net.sf.log4jdbc.sql.jdbcapi.DriverSpy
    jdbc-url: jdbc:log4jdbc:postgresql://localhost:5432/postgres?charSet=UTF-8
    username: postgres
    password: postgres

---
# dev
spring:
  config:
    activate:
      on-profile: dev
  datasource:
    driver-class-name: net.sf.log4jdbc.sql.jdbcapi.DriverSpy
    jdbc-url: jdbc:log4jdbc:postgresql://localhost:5432/postgres?charSet=UTF-8
    username: postgres
    password: postgres

---
# prod
spring:
  config:
    activate:
      on-profile: prod
  datasource:
    driver-class-name: net.sf.log4jdbc.sql.jdbcapi.DriverSpy
    jdbc-url: jdbc:log4jdbc:postgresql://localhost:5432/postgres?charSet=UTF-8
    username: postgres
    password: postgres
```

## 3. 실행 확인

![local](/assets/img/2022-04-26-spring-boot-separating-one-yml-file/local.png)

![dev](/assets/img/2022-04-26-spring-boot-separating-one-yml-file/dev.png)
