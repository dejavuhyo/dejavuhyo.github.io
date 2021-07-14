---
title: Spring Boot Log4jdbc 설정
author: dejavuhyo
date: 2020-10-29 14:30:00 +0900
categories: [Application, Architecture]
tags: [log4jdbc, spring-boot-log4jdbc, log, database-log, 스프링부트-데이터베이스-로그, 데이터베이스-로그, 스프링부트-로그, 로그]
---

## 1. Log4jdbc Download

* Maven

```xml
<dependency>
    <groupId>org.bgee.log4jdbc-log4j2</groupId>
    <artifactId>log4jdbc-log4j2-jdbc4.1</artifactId>
    <version>1.16</version>
</dependency>
```

## 2. 설정

### 1) log4jdbc.log4j2.properties

* **경로:** src/main/resources/log4jdbc.log4j2.properties

```properties
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
log4jdbc.dump.sql.maxlinelength=0
```

### 2) application.properties

* **경로:** src/main/resources/application.properties

```properties
spring.datasource.driver-class-name=net.sf.log4jdbc.sql.jdbcapi.DriverSpy
spring.datasource.jdbc-url=jdbc:log4jdbc:postgresql://localhost:5432/postgres?charSet=UTF-8
spring.datasource.username=postgres
spring.datasource.password=password
```

## 3. 적용

### 1) logback-spring.xml 추가

* **경로:** src/main/resources/logback-spring.xml

* **logback-spring.xml 상세 설정:** <https://dejavuhyo.github.io/posts/spring-boot-logback/>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds" debug="true">
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{5} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <logger name="jdbc" level="off"/>
    <logger name="jdbc.sqlonly" level="off"/>
    <logger name="jdbc.sqltiming" level="debug"/>
    <logger name="jdbc.audit" level="off"/>
    <logger name="jdbc.resultset" level="off"/>
    <logger name="jdbc.resultsettable" level="debug"/>
    <logger name="jdbc.connection" level="off"/>

    <root level="info">
        <appender-ref ref="CONSOLE" />
    </root>
</configuration>
```

## 4. 결과

![img001](/assets/img/2020-10-29-spring-boot-log4jdbc/img001.png)

## [출처 및 참고]
* <https://kutar37.tistory.com/entry/log4jdbc-%EC%A0%81%EC%9A%A9>
* <https://4coops.tistory.com/entry/Spring-Boot-MyBatis-%EC%97%90%EC%84%9C-SQL-Result-%EB%A1%9C%EA%B7%B8%EC%B0%8D%EA%B8%B0>
