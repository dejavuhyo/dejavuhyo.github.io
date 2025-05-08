---
title: Ubuntu에 OpenJDK 17 설치
author: dejavuhyo
date: 2024-08-27 08:35:00 +0900
categories: [DevOps, CI/CD]
tags: [ubuntu-openjdk ubuntu-jdk, openjdk, openjdk-install]
---

## 1. 버전 확인
설치 가능한 openjdk 버전을 확인한다.

```shell
# 모든 버전 조회
$ sudo apt list openjdk*

# 특정 버전 조회
$ sudo apt list openjdk-[버전]*
```

## 2. 설치

```shell
$ sudo apt install openjdk-17-jdk
```

## 3. 설치 확인

```shell
$ java --version
```

## 4. 환경변수 설정

```shell
sudo vim /etc/profile
```

하단에 추가한다.

```text
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64 # Java 설치경로
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib/ext:$JAVA_HOME/lib/tools.jar
```

설정 파일의 변경사항을 적용한다.

```shell
$ source /etc/profile
```

환경변수 설정을 확인한다.

```shell
$ echo $JAVA_HOME
/usr/lib/jvm/java-17-openjdk-amd64
```

## [출처 및 참고]
* [https://velog.io/@ung6860/개발환경Ubuntu에-Java-설치하기](https://velog.io/@ung6860/개발환경Ubuntu에-Java-설치하기)
