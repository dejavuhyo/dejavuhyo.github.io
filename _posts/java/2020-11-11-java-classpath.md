---
title: Java 환경변수 설정
author: dejavuhyo
date: 2020-11-11 11:47:00 +0900
categories: [Language, Java]
tags: [java-home, java-classpath, classpath, 자바-홈, 자바-클래스패스, 클래스패스, 자바-환경변수, 환경변수]
---

## 1. Path 설정

### 1) 환경 변서 설정 이동

* 시스템 속성 → 환경 변수

![img001](/assets/img/2020-11-11-java-classpath/img001.png)

![img002](/assets/img/2020-11-11-java-classpath/img002.png)

### 2) JAVA_HOME 설정

* 시스템 변수 → 새로 만들기
  - 변수 이름: JAVA_HOME
  - 변수 값: C:\java-11-openjdk-11.0.12.7-1

![img003](/assets/img/2020-11-11-java-classpath/img003.png)

### 3) Path 설정

* Path 선택 → 편집 → 새로 만들기
  - %JAVA_HOME%\bin

![img004](/assets/img/2020-11-11-java-classpath/img004.png)

![img005](/assets/img/2020-11-11-java-classpath/img005.png)

### 4) CLASSPATH 설정

* 시스템 변수 → 새로 만들기
  - 변수 이름: CLASSPATH
  - 변수 값: %JAVA_HOME%\lib

![img006](/assets/img/2020-11-11-java-classpath/img006.png)

![img007](/assets/img/2020-11-11-java-classpath/img007.png)

## 2. 확인

```command
C:\Users\user>echo %JAVA_HOME%
C:\Users\user>java -version
C:\Users\user>javac
```

![img008](/assets/img/2020-11-11-java-classpath/img008.png)

## [출처 및 참고]
* [https://hyoje420.tistory.com/7](https://hyoje420.tistory.com/7)
