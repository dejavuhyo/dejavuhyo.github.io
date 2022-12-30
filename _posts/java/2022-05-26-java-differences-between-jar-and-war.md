---
title: Java JAR와 WAR의 차이점
author: dejavuhyo
date: 2022-05-26 16:40:00 +0900
categories: [Language, Java]
tags: [java-jar-vs-war, jar-vs-war, java-jar-war, jar-war, jar-war-차이점]
---

## 1. 개념
기본적으로 JAR, WAR 모두 Java의 jar 옵션 (java -jar)을 이용해 생성된 압축(아카이브) 파일로, 애플리케이션을 쉽게 배포하고 동작시킬 수 있도록 관련 파일(리소스, 속성 파일 등)을 패키징한 것이다.

## 2. JAR (Java Archive)
JAVA 애플리케이션이 동작할 수 있도록 자바 프로젝트를 압축한 파일이다. Class(JAVA 리소스, 속성 파일), 라이브러리 파일을 포함한다. JRE(JAVA Runtime Environment)만 있어도 실행할 수 있다.

```shell
$ java -jar file_name.jar
```

## 3. WAR (Web Application Archive)
`Servlet/Jsp` 컨테이너에 배치할 수 있는 웹 애플리케이션(Web Application) 압축파일 포맷으로 웹 관련 자원을 포함한다. (JSP, Servlet, JAR, Class, XML, HTML, Javascript)

사전 정의된 구조를 사용하며(WEB-INF, META-INF) 별도의 웹서버(WEB) 혹은 웹 컨테이너(WAS) 필요하다.

즉, JAR 파일의 일종으로 웹 애플리케이션 전체를 패키징하기 위한 JAR 파일이다.

## 4. 압축 구조

![compression-structure](/assets/img/2022-05-26-java-differences-between-jar-and-war/compression-structure.png)

## [출처 및 참고]
* <https://hye0-log.tistory.com/27>
* <https://docs.spring.io/spring-boot/docs/3.0.x/reference/htmlsingle/>
* <https://programmer93.tistory.com/40>
