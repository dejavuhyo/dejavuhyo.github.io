---
title: Spring Boot Logback 설정
author: dejavuhyo
date: 2020-10-28 13:30:00 +0900
categories: [Application, Framework]
tags: [logback, spring-boot-logback, log, 로그백, 스프링부트-로그백, 스프링부트-로그, 로그]
---

## 1. LogBack이란
Log4j를 만든 개발자 Ceki Gülcü가 Log4j를 기반으로 더 빠른 속도와 더 적은 메모리 점유율 등의 성능을 개발하여 만든 Logging Framework이다.

* **LogBack Manual:** <http://logback.qos.ch/manual/index.html>

## 2. LogBack 특징

### 1) Level
로그에 레벨을 설정할 수 있다. 개발 시 디버깅을 위해 출력하도록 한 로그들은 개발 완료 후 운영 시점에서는 더 이상 출력하지 않게 한다. 로그마다 레벨을 설정해두고 설정 파일에서 출력 로그 레벨을 설정하여 원하는 단계의 로그만 출력할 수 있다.

### 2) Appender
출력 방법을 선택할 수 있다. 로그의 기록을 담당하는 Appender에게 출력 위치(콘솔, 파일 등)나 출력 내용(날짜/시간, 레벨 등)에 대한 패턴을 설정할 수 있다.

* **ConsoleAppender:** 콘솔에 로그를 기록하는 방법
* **FileAppender:** 파일에 로그를 기록하는 방법
* **RollingFileAppender:** 여러 개의 파일을 순회하며 로그를 기록하는 방법
* **SMTPAppender:** 로그를 메일로 기록하는 방법
* **DBAppender:** 데이터베이스에 로그를 기록하는 방법

### 3) Logger
로그마다 다른 설정 내용을 쉽게 적용시킬 수 있다. 위의 주요 설정을 포함한 다양한 설정 항목을 가지고 있는 객체(Logger)에 이름을 부여하여 필요한 상황에 맞게 적절한 Logger를 호출하여 사용할 수 있다.

### 4) Automatic Reloading Configuration File
설정 파일을 스캔하는 별도의 스레드를 두어 지정한 시간마다 설정 파일을 스캔해 프로그램의 재시작 없이 설정을 변경할 수 있다.

### 5) Automatic Compression / Removal
로그 파일을 생성할 때, 별도의 프로그램을 통해 압축을 진행할 필요 없이 자동 압축을 지원하며, 시간 또는 개수를 설정하여 설정한 시간이나 개수를 초과할 경우 로그 파일이 자동으로 삭제되도록 할 수 있다.

### 6) Graceful Recovery From I/O Failures
로그 기록 중에 장애가 발생할 경우, 서버 중지 없이 장애 발생시점으로부터의 자동 복구를 지원한다.

### 7) If Statement
설정 파일에 조건문을 사용하여 세밀하게 로깅 설정하여 여러 환경에 맞게 자동 구성되도록 할 수 있다.

### 8) 패턴에 사용되는 요소

* **%Logger{length}:** Logger name을 축약(length는 최대 자리 수)
* **%thread:** 현재 스레드 이름
* **%-5level:** 로그 레벨, -5는 출력의 고정폭 값
* **%msg:** 로그 메시지
* **%n:** 개행 문자
* **%d:** 로그 기록시간
* **%p:** 로깅 레벨
* **%F:** 로깅이 발생한 프로그램 파일명
* **%M:** 로깅일 발생한 메소드의 이름
* **%l:** 로깅이 발생한 호출지의 정보
* **%L:** 로깅이 발생한 호출지의 라인 수
* **%t:** 쓰레드 명
* **%c:** 로깅이 발생한 카테고리
* **%C:** 로깅이 발생한 클래스 명
* **%m:** 로그 메시지
* **%r:** 애플리케이션 시작 이후부터 로깅이 발생한 시점까지의 시간

### 9) Log Level

> ERROR < WARN < INFO < DEBUG < TRACE

* **ERROR:** 요청을 처리하는 중 오류가 발생한 경우 표시한다.
* **WARN:** 처리 가능한 문제, 향후 시스템 에러의 원인이 될 수 있는 경고성 메시지를 나타낸다.
* **INFO:** 상태변경과 같은 정보성 로그를 표시한다.
* **DEBUG:** 프로그램을 디버깅하기 위한 정보를 표시한다.
* **TRACE:** 추적 레벨은 Debug보다 훨씬 상세한 정보를 나타낸다.

## 3. 적용
spring-boot-starter-web 안에 spring-boot-starter-logging에 구현체가 있다.

Spring Boot 구동 시 자동으로 `logback.xml` 파일이 있는지 탐색하고 logback 설정을 적용한다.

* **경로:** src/main/resources/logback.xml

* logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds" debug="true">

    <property name="LOGS_ABSOLUTE_PATH" value="logs"/>

    <!-- 콘솔로 로그를 남김 -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <charset>UTF-8</charset>
            <!-- 로그 메시지 패턴 -->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{5} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- 파일로 로그를 남김 -->
    <appender name="ROLLFILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOGS_ABSOLUTE_PATH}/logback.log</file>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{5} - %msg%n</pattern>
        </encoder>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOGS_ABSOLUTE_PATH}/logback.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!-- or whenever the file size reaches 100MB -->
                <!-- kb, mb, gb -->
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>

            <!-- 30일 -->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <!-- 에러 일 경우 파일에 로그를 남김 -->
    <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>error</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <file>${LOG_DIR}/bamdule-error.log</file>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{5} - %msg%n</pattern>
        </encoder>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOGS_ABSOLUTE_PATH}/logback.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <!-- Database Section -->
    <logger name="org.apache.commons.dbcp2" level="error" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
        <appender-ref ref="ERROR"/>
    </logger>
    <logger name="java.sql" level="warn" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="jdbc" level="error" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
        <appender-ref ref="ERROR"/>
    </logger>
    <!-- SQL문과 해당 SQL을 실행시키는데 수행된 시간 정보(milliseconds)를 포함 -->
    <logger name="jdbc.sqltiming" level="debug" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="jdbc.resultsettable" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <!-- netflix -->
    <logger name="com.netflix.discovery" level="error" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
        <appender-ref ref="ERROR"/>
    </logger>
    <!-- Hikari CP -->
    <logger name="com.zaxxer.hikari.pool.HikariPool" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>

    <!-- Application Section -->
    <logger name="pbb" level="debug" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>

    <!-- Framework Section -->
    <logger name="egovframework" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="org.springframework" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="org.springframework.scheduling" level="info" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>
    <logger name="org.springframework.web" level="debug" additivity="false">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </logger>

    <!-- root는 글로벌 로거를 의미하며, 위의 logger에 해당하지 않으면 root 로거가 실행 -->
    <root level="info">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ROLLFILE"/>
    </root>

</configuration>
```

## [출처 및 참고]
* [http://logback.qos.ch/manual/index.html](http://logback.qos.ch/manual/index.html)
* [https://kchanguk.tistory.com/48](https://kchanguk.tistory.com/48)
* [https://goddaehee.tistory.com/206](https://goddaehee.tistory.com/206)
* [https://bamdule.tistory.com/29](https://bamdule.tistory.com/29)
