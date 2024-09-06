---
title: Ubuntu에 Zip 파일로 SonarQube 설치
author: dejavuhyo
date: 2024-09-06 09:30:00 +0900
categories: [DevOps, StaticAnalysis]
tags: [ubuntu-sonarqube, sonarqube-zip-file, sonarqube-install, 우분투-소나큐브, 소나큐브-zip-파일, 소나큐브-설치]
---

## 1. 배포판 다운로드
[배포판](https://www.sonarsource.com/products/sonarqube/downloads/?_gl=1*1sl5cnh*_gcl_au*NDA1MTYxMTYyLjE3MjIzMDAzNTY.*_ga*OTc1NzIzNzkyLjE3MjIzMDAzNTY.*_ga_9JZ0GZ5TC6*MTcyMjMwMDM1Ni4xLjEuMTcyMjMwMjM0NS42MC4wLjA.)을 다운로드하고 압축을 푼다(숫자로 시작하는 디렉토리에 압축을 풀지 않는다).

`<sonarqubeHome>`(아래)는 SonarQube 배포판이 압축 해제된 디렉토리 경로를 나타낸다.

## 2. 설정

### 1) 데이터베이스 액세스 설정
데이터베이스 설정을 구성하려면 `<sonarqubeHome>/conf/sonar.properties`를 편집한다. 템플릿은 지원되는 모든 데이터베이스에 사용할 수 있다. 필요한 템플릿의 주석을 해제하고 구성하고 H2에 전용된 줄을 주석 처리하기만 하면 된다.

```properties
# Example for PostgreSQL with default "public" schema and default TCP port 5432
sonar.jdbc.username=sonarqube
sonar.jdbc.password=mypassword
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
```

### 2) JDBC 드라이버 추가
지원되는 데이터베이스(Oracle 제외)의 드라이버는 이미 제공된다. 제공된 드라이버를 대체하지 않는다. 지원되는 드라이버는 이것뿐이다.

Oracle의 경우 JDBC 드라이버를 `<sonarqubeHome>/extensions/jdbc-driver/oracle`에 복사하여 넣는다.

### 3) Elasticsearch 저장 경로 구성
기본적으로 Elasticsearch 데이터는 `<sonarqubeHome>/data`에 저장되지만 프로덕션 인스턴스에는 권장되지 않는다. 대신 이 데이터를 다른 곳, 이상적으로는 빠른 I/O가 있는 전용 볼륨에 저장해야 한다. 허용 가능한 성능을 유지하는 것 외에도 SonarQube 업그레이드도 용이해진다.

다음 설정을 구성하려면 `<sonarqubeHome>/conf/sonar.properties`를 편집한다.

```properties
sonar.path.data=/var/sonarqube/data
sonar.path.temp=/var/sonarqube/temp
```

SonarQube를 시작하는데 사용되는 사용자는 해당 디렉토리에 대한 읽기 및 쓰기 액세스 권한이 있어야 한다.

### 4) Java 실행 파일 경로 조정
기본적으로 스크립트는 PATH에서 사용 가능한 Java 실행 파일을 사용한다. 서버에 여러 버전의 Java가 설치된 경우 어떤 버전의 Java를 사용할지 명시적으로 정의해야 할 수 있다.

`SONAR_JAVA_PATH` 환경 변수를 설정하여 기본 Java 실행 파일을 덮어쓸 수 있다.

* Linux

```text
export SONAR_JAVA_PATH="path/to/java_home/bin/java"
```

* Windows

```text
setx SONAR_JAVA_PATH "C:\Program Files\java_home\bin\java.exe"
```

## 3. 웹 서버 시작
다음 스크립트를 실행하여 서버를 시작한다.

* Linux: `<sonarqubeHome>/bin/linux-x86-64/sonar.sh start`

* macOS: `<sonarqubeHome>/bin/macosx-universal-64/sonar.sh start`

* Windows: `<sonarqubeHome>\bin\windows-x86-64\StartSonar.bat`

`http://localhost:9000`에서 SonarQube를 찾아볼 수 있다(기본 시스템 관리자 자격 증명은 `admin`/`admin` 이다).

## [출처 및 참고]
* [https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/installing-sonarqube-from-zip-file/](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/installing-sonarqube-from-zip-file/)
