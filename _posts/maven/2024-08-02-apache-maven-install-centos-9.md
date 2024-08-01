---
title: CentOS 9에 Apache Maven 설치
author: dejavuhyo
date: 2024-08-02 08:40:00 +0900
categories: [DevOps, Maven]
tags: [maven, apache-maven, centos-maven, maven-install, 메이븐-설치]
---

## 1. Apache Maven 다운로드
최신 버전의 Apache Maven을 다운로드 한다. 최신 버전은 [Apache Maven 공식 웹사이트](https://maven.apache.org/download.cgi)에서 확인한다.

wget 명령을 사용하여 Maven을 다운로드할 수 있다. 시스템에 wget이 설치되어 있지 않으면

```shell
$ sudo dnf install wget
```

또는

```shell
$ sudo dnf install wget
```

사용하여 설치할 수 있다.

그런 다음 다음을 사용하여 Maven을 다운로드 한다.

```shell
$ cd /opt
$ wget https://dlcdn.apache.org/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz
```

## 2. Maven 추출 및 설치
다운로드 후 Maven 아카이브를 `/opt` 디렉토리나 원하는 다른 디렉토리에 추출한다.

```shell
$ tar zxvf apache-maven-3.9.8-bin.tar.gz
```

## 3. 환경 변수 구성
Maven을 전역적으로 사용하려면 `M2_HOME` 및 `PATH` 환경 변수를 설정해야 한다. `/etc/profile.d` 디렉토리에 새 파일을 만들어서 할 수 있다.

```shell
$ sudo vi /etc/profile.d/maven.sh
```

다음 줄을 파일에 추가한다.

```text
export M2_HOME=/opt/apache-maven-3.9.8
export PATH=${M2_HOME}/bin:${PATH}
```

스크립트를 실행 가능하게 만들고 새로운 환경 변수를 로드한다.

```shell
$ sudo chmod +x /etc/profile.d/maven.sh
$ source /etc/profile.d/maven.sh
```

## 4. 설치 확인
Maven이 올바르게 설치되었는지 확인한다.

```shell
$ mvn -version
```

## [출처 및 참고]
* [https://tecadmin.net/install-apache-maven-on-centos/](https://tecadmin.net/install-apache-maven-on-centos/)
* [https://www.virtono.com/community/tutorial-how-to/how-to-install-apache-maven-on-centos-7/](https://www.virtono.com/community/tutorial-how-to/how-to-install-apache-maven-on-centos-7/)
