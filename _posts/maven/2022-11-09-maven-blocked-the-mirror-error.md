---
title: Maven Blocked the mirror 에러
author: dejavuhyo
date: 2022-11-08 21:20:00 +0900
categories: [DevOps, Maven]
tags: [maven-blocked-mirror, maven-blocked, blocked-mirror, maven-http-repositories, http-repositories-blocking, maven-381-version, maven-default-http-blocker, http-blocker, maven-blocked-mirror-에러, maven-에러]
---

## 1. Maven 3.8.1
Maven 3.8.1 버전에서 http에 대한 외부 연결을 막는 설정이 default로 변경되었다.

* <https://maven.apache.org/docs/3.8.1/release-notes.html>

## 2. 문제점
`mvn clean package` 명령 실행 시 Maven에서 http uri를 block 하여 내부망에 존재하는 http에 접속할 수 없어 `failed to collect dependencies ... [maven-default-http-blocker]...` error와 함께 build에 실패한다.

## 3. 해결방법
`apache-maven-3.8.4\conf` 경로에 있는 `settings.xml` 파일을 수정한다.

* <https://stackoverflow.com/questions/67001968/how-to-disable-maven-blocking-external-http-repositories>

### 1) settings.xml 열기
`settings.xml` 파일을 열고 `maven-default-http-blocker`를 검색한다.

```xml
<mirror>
    <id>maven-default-http-blocker</id>
    <mirrorOf>external:http:*</mirrorOf>
    <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
    <url>http://0.0.0.0/</url>
</mirror>
```

### 2-1) 첫 번째 방법: 주석처리
`maven-default-http-blocker`를 주석처리 한다.

```xml
<!--
<mirror>
    <id>maven-default-http-blocker</id>
    <mirrorOf>external:http:*</mirrorOf>
    <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
    <url>http://0.0.0.0/</url>
</mirror>
-->
```

### 2-2) 두 번째 방법: http blocker 무력화
http를 무력화 시켜주는 코드를 작성한다.

```xml
<mirror>
    <id>maven-default-http-blocker</id>
    <mirrorOf>external:dont-match-anything-mate:*</mirrorOf>
    <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
    <url>http://0.0.0.0/</url>
</mirror>
```

## [출처 및 참고]
* <https://jjam89.tistory.com/201>
* <https://stackoverflow.com/questions/67001968/how-to-disable-maven-blocking-external-http-repositories>
* <https://velog.io/@rosa/maven-Blocked-the-mirror-%EC%97%90%EB%9F%AC>
* <https://backstage.forgerock.com/knowledge/kb/article/a15127846>
* <https://bkjeon1614.tistory.com/700>
