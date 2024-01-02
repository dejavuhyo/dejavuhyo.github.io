---
title: Spring Boot Log4jdbc 설정시 데이터베이스 jdbc-url
author: dejavuhyo
date: 2022-04-29 10:15:00 +0900
categories: [Framework, Spring]
tags: [spring-log4jdbc, log4jdbc, jdbc-url, log4jdbc-jdbc-url, database-jdbc-url, jdbc-url-목록, 데이터베이스-jdbc-url]
---

## 1. Log4jdbc 설정

* Spring Boot Log4jdbc 설정: [https://dejavuhyo.github.io/posts/spring-boot-log4jdbc/](https://dejavuhyo.github.io/posts/spring-boot-log4jdbc/)

## 2. driver-class-name 설정

* 수정 전

```properties
# PostgreSQL Driver
spring.datasource.driver-class-name=org.postgresql.Driver
```

* 수정 후

```properties
spring.datasource.driver-class-name=net.sf.log4jdbc.sql.jdbcapi.DriverSpy
```

## 3. jdbc-url 설정
`log4jdbc`를 추가한다.

* 수정 전

```properties
# PostgreSQL
jdbc:postgresql://localhost:5432/postgres?charSet=UTF-8

# Oracle
jdbc:oracle:thin:@localhost:1521/xe

# MariaDB
jdbc:mariadb://localhost:3306/test?characterEncoding=UTF-8

# MySQL
jdbc:mysql://localhost:3306/test?characterEncoding=UTF-8

# SQLServer
jdbc:sqlserver://localhost:1433;DatabaseName=AdventureWorks;integratedSecurity=true
```

* 수정 후

```properties
# PostgreSQL
jdbc:log4jdbc:postgresql://localhost:5432/postgres?charSet=UTF-8

# Oracle
jdbc:log4jdbc:oracle:thin:@localhost:1521/xe

# MariaDB
jdbc:log4jdbc:mariadb://localhost:3306/test?characterEncoding=UTF-8

# MySQL
jdbc:log4jdbc:mysql://localhost:3306/test?characterEncoding=UTF-8

# SQLServer
jdbc:log4jdbc:sqlserver://localhost:1433;DatabaseName=AdventureWorks;integratedSecurity=true
```
