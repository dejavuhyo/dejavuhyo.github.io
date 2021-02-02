---
title: Windows에서 Tomcat 콘솔 로그 한글깨짐 해결
author: dejavuhyo
date: 2020-05-21 15:00:00 +0900
categories: [Application, Architecture]
tags: [tomcat-encoding, tomcat-windows-encoding, 톰캣-인코딩, 톰캣-윈도우-인코딩, 톰캣-윈도우-한글깨짐]
---

## 1. 원인
Windows10의 콘솔 인코딩과 Tomcat 로그의 인코딩이 달라서 문제가 발생

![img001](/assets/img/2020-05-21-tomcat-console-encoding/img001.png)

## 2. 버전 확인
Tomcat의 logging.properties 파일에 인코딩 설정이 추가된 버전 (버전별 ChangeLog 확인)

* **Tomcat7:** v7.0.92 이상
* **Tomcat8.5:** v8.5.36 이상
* **Tomcat9:** v9.0.14 이상

## 3. 콘솔 확인

### 1) 콘솔 인코딩 한국어 설정 확인

* chcp

![img002](/assets/img/2020-05-21-tomcat-console-encoding/img002.png)

### 2) Tomcat 콘솔 속성 확인

![img003](/assets/img/2020-05-21-tomcat-console-encoding/img003.png)

## 4. 해결방법
apache-tomcat-9.0.33\conf\logging.properties 파일의 인코딩 설정을 UTF-8에서 EUC-KR로 변경

```properties
############################################################
# Handler specific properties.
# Describes specific configuration info for Handlers.
############################################################
 
1catalina.org.apache.juli.AsyncFileHandler.level = FINE
1catalina.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
1catalina.org.apache.juli.AsyncFileHandler.prefix = catalina.
1catalina.org.apache.juli.AsyncFileHandler.maxDays = 90
1catalina.org.apache.juli.AsyncFileHandler.encoding = EUC-KR
 
2localhost.org.apache.juli.AsyncFileHandler.level = FINE
2localhost.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
2localhost.org.apache.juli.AsyncFileHandler.prefix = localhost.
2localhost.org.apache.juli.AsyncFileHandler.maxDays = 90
2localhost.org.apache.juli.AsyncFileHandler.encoding = EUC-KR
 
3manager.org.apache.juli.AsyncFileHandler.level = FINE
3manager.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
3manager.org.apache.juli.AsyncFileHandler.prefix = manager.
3manager.org.apache.juli.AsyncFileHandler.maxDays = 90
3manager.org.apache.juli.AsyncFileHandler.encoding = EUC-KR
 
4host-manager.org.apache.juli.AsyncFileHandler.level = FINE
4host-manager.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
4host-manager.org.apache.juli.AsyncFileHandler.prefix = host-manager.
4host-manager.org.apache.juli.AsyncFileHandler.maxDays = 90
4host-manager.org.apache.juli.AsyncFileHandler.encoding = EUC-KR
 
java.util.logging.ConsoleHandler.level = FINE
java.util.logging.ConsoleHandler.formatter = org.apache.juli.OneLineFormatter
java.util.logging.ConsoleHandler.encoding = EUC-KR
```

## 5. Tomcat 실행 로그 확인

![img004](/assets/img/2020-05-21-tomcat-console-encoding/img004.png)

## 출처 및 참고
* <https://1004lucifer.blogspot.com/2020/02/tomcat-windows.html>
