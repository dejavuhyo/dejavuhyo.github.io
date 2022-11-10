---
title: IntelliJ Maven Blocked the mirror 에러
author: dejavuhyo
date: 2022-11-10 20:00:00 +0900
categories: [Application, IDE]
tags: [maven-blocked-mirror, maven-blocked, blocked-mirror, maven-http-repositories, http-repositories-blocking, maven-381-version, maven-default-http-blocker, http-blocker, maven-blocked-mirror-에러, maven-에러]
---

## 1. Maven 3.8.1
Maven 3.8.1 버전에서 http에 대한 외부 연결을 막는 설정이 default로 변경되었다.

* <https://maven.apache.org/docs/3.8.1/release-notes.html>

## 2. 문제점
내부망에 존재하는 http에 접속할 수 없어 `Blocked the mirror` error와 함께 build에 실패한다.

## 3. 해결방법
IntelliJ Maven 설정을 `Use Maven wrapper`로 변경한다.

> Bundled (Maven 3)은 3.8.1 버전 설정이고, Use Maven wrapper는 properties 파일에서 읽어오는 이전 버전이다.

* File → Settings → Build, Execution, Deployment → Build Tools → Maven

![intellij-settings](/assets/img/2022-11-10-intellij-maven-blocked-the-mirror-error/intellij-settings.png)

## [출처 및 참고]
* <https://velog.io/@rosa/maven-Blocked-the-mirror-%EC%97%90%EB%9F%AC>
* <https://jjam89.tistory.com/201>
* <https://stackoverflow.com/questions/67001968/how-to-disable-maven-blocking-external-http-repositories>
