---
title: Windows에서 jar 실행하는 bat 파일
author: dejavuhyo
date: 2023-01-26 20:40:00 +0900
categories: [Application, Architecture]
tags: [windows-bat, windows-batch, batch-jar, bat-jar, bat, batch, bat-jar-run, bat-파일, jar-실행, 배치파일, 윈도우-배치파일]
---

## 1. 시작

* start.bat

```shell
@echo off
title "API START"
set JAVA_PATH=C:\Java\jdk-17.0.5+8\bin
set START_PATH=C:\api

cd %START_PATH%
%JAVA_PATH%\java.exe -Dfile.encoding=UTF-8 -jar api-0.0.1-SNAPSHOT.jar

pause 
exit
```

## 2. 정지
`java.exe` 프로세스를 종료한다.

* stop.bat

```shell
@echo off
title "API STOP"
taskkill /im java.exe /f

pause
exit
```

## 3. 설정 옵션

### 1) 콘솔창이 없어지지 않음
System.out.print/println 같은 콘솔 명령어를 실행할 때에는 java 명령어를 사용하고 IzPack, JFrame 같은 GUI 프로그램은 javaw 명령어로 실행한다.

```shell
\jdk-17.0.5+8\bin\java.exe -jar api-0.0.1-SNAPSHOT.jar
```

### 2) 실행 시 콘솔창이 없어짐

```shell
start \jdk-17.0.5+8\bin\javaw.exe -jar api-0.0.1-SNAPSHOT.jar
```

### 3) 프로세스 상세 목록

```shell
tasklist -v
```

### 4) 프로세스 ID로 강제 종료

```shell
taskkill /pid [PID] /f
```

### 5) 프로세스 이름으로 강제 종료

```shell
taskkill /im [프로세스 이름] /f
```

## [출처 및 참고]
* [https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=whydda&logNo=221397878157](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=whydda&logNo=221397878157)
* [https://blog.naver.com/PostView.naver?blogId=moonlight_youlike&logNo=221388091213&parentCategoryNo=&categoryNo=13&viewDate=&isShowPopularPosts=true&from=search](https://blog.naver.com/PostView.naver?blogId=moonlight_youlike&logNo=221388091213&parentCategoryNo=&categoryNo=13&viewDate=&isShowPopularPosts=true&from=search)
* [https://youngwonhan-family.tistory.com/entry/Windows%EC%97%90%EC%84%9C-bat%ED%8C%8C%EC%9D%BC%EB%A1%9C-FatJar-%EC%8B%A4%ED%96%89-%EB%B0%8F-%EC%A4%91%EC%A7%80%ED%95%98%EA%B8%B0](https://youngwonhan-family.tistory.com/entry/Windows%EC%97%90%EC%84%9C-bat%ED%8C%8C%EC%9D%BC%EB%A1%9C-FatJar-%EC%8B%A4%ED%96%89-%EB%B0%8F-%EC%A4%91%EC%A7%80%ED%95%98%EA%B8%B0)
