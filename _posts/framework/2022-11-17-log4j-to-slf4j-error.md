---
title: log4j-slf4j-impl cannot be present with log4j-to-slf4j 에러
author: dejavuhyo
date: 2022-11-17 21:20:00 +0900
categories: [Application, Framework]
tags: [log4j-slf4j-impl, log4j, slf4j, log4j-to-slf4j, log4j-error, slf4j-error, log4j-to-slf4j-에러, log4j-에러, slf4j-에러]
---

## 1. 에러 메시지

```text
Caused by: org.apache.logging.log4j.LoggingException: log4j-slf4j-impl cannot be present with log4j-to-slf4j
```

## 2. 해결방법

### 1) Maven
스프링 부트는 클래스 경로에 있는 경우 로깅 구성을 위해 Log4j2를 지원한다. 종속 항목을 조립하기 위해 starter를 사용하는 경우 Logback을 제외하고 log4j2를 포함해야 한다.

spring-jcl starter를 사용하지 않는 경우 Log4j2에 추가로 제공해야 한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

### 2) Gradle
Gradle은 스타터를 설정하는 몇 가지 다른 방법을 제공한다. 한 가지 방법은 모듈 교체를 사용하는 것이다. 이렇게 하려면 Log4j2 스타터에 대한 종속성을 선언하고 기본 로깅 스타터의 모든 발생을 Log4j2 스타터로 대체해야 한다고 Gradle에 알린다.

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-log4j2'
}

configurations {
   all {
       exclude group: 'org.springframework.boot', module: 'spring-boot-starter-logging'
   }
}
```

## [출처 및 참고]
* <https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto.logging.log4j>
* <https://stackoverflow.com/questions/59629214/caused-by-org-apache-logging-log4j-loggingexception-log4j-slf4j-impl-cannot-be>
