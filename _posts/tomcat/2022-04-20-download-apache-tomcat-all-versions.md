---
title: 아파치 톰캣 모든 버전 다운로드
author: dejavuhyo
date: 2022-04-20 14:20:00 +0900
categories: [DevOps, Tomcat]
tags: [apache-tomcat-all-versions, apache-tomcat, tomcat, tomcat-download, tomcat-all-versions, 아파치-톰캣, 아파치-톰캣-다운로드, 아파치-톰캣-모든-버전, 톰캣-다운로드]
---

## 1. 모든 버전 다운로드 사이트

* <http://archive.apache.org/dist/tomcat/>

## 2. 다운로드 방법

### 1) Tomcat 메인 버전 선택

![tomcat-version](/assets/img/2022-04-20-download-apache-tomcat-all-versions/tomcat-version.png)

### 2) 하위 버전 선택

![subversion](/assets/img/2022-04-20-download-apache-tomcat-all-versions/subversion.png)

### 3) bin 폴더 선택
Packaging 상세 설명은 아래와 같다.

* bin 폴더
  - `apache-tomcat-[version].zip or .tar.gz`: 기본 배포. 이러한 배포에는 Windows 서비스 래퍼나 Windows용으로 컴파일된 APR/네이티브 라이브러리가 포함되지 않다.
  - `apache-tomcat-[version].exe`: Tomcat용 32비트/64비트 Windows 설치 프로그램. 이 배포판에는 대부분의 기본 배포판이 포함되어 있지만 Tomcat 실행을 위한 일부 명령줄 스크립트는 포함되어 있지 않다. 이 배포는 Windows 바로 가기 또는 서비스를 통해 Tomcat을 시작하려는 사용자를 대상으로 한다.
  - `apache-tomcat-[version]-windows-x64.zip`: x64 Windows 플랫폼에서 64비트 JVM과 함께 사용하기 위해 Windows 서비스 래퍼 및 컴파일된 APR/네이티브 라이브러리를 포함하는 64비트 Windows 전용 배포이다.
  - `apache-tomcat-[version]-deployer.zip or .tar.gz`: 독립 실행형 Tomcat 웹 응용 프로그램 배포자.
  - `apache-tomcat-[version]-fulldocs.tar.gz`: 완전한 javadoc을 포함한 Tomcat 문서 번들.

* src 폴더
  - `apache-tomcat-[version].zip or .tar.gz`: 소스 코드.

![bin](/assets/img/2022-04-20-download-apache-tomcat-all-versions/bin.png)

### 4) 다운로드
플랫폼에 맞는 tomcat을 다운로드 한다.

![download](/assets/img/2022-04-20-download-apache-tomcat-all-versions/download.png)

## [출처 및 참고]
* [https://archive.apache.org/dist/tomcat/](https://archive.apache.org/dist/tomcat/)
