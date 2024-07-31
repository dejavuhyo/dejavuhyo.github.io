---
title: CentOS 9에 OpenJDK 17 설치
author: dejavuhyo
date: 2024-07-30 09:33:00 +0900
categories: [DevOps, Linux]
tags: [centos-openjdk, centos-jdk, openjdk, openjdk-install]
---

## 1. 운영 체제 확인

```shell
$ cat /etc/*release
```

## 2. 패키지 저장소 업데이트

```shell
$ sudo dnf update
```

## 3. 설치된 JDK 확인

```shell
$ dnf list installed | grep java
```

## 4. 설치 가능한 JDK 버전 확인

```shell
$ dnf list java*jdk-devel
```

## 5. 설치

```shell
$ dnf install java-17-openjdk-devel.x86_64
```

## 6. Java 버전 확인

```shell
$ java -version
```

## 5. Java 홈 경로 설정 확인

```shell
$ echo $JAVA_HOME
```

## 6. Java 홈 경로 설정

### 1) javac 위치 확인

```shell
$ which javac
/usr/bin/javac
```

### 2) 원본 파일 위치 확인
`/usr/bin/javac`는 심볼릭 링크 이므로, 원본 파일 위치 확인

```shell
$ readlink -f /usr/bin/javac
/usr/lib/jvm/java-17-openjdk-17.0.6.0.10-3.el9.x86_64/bin/javac
```

### 3) JAVA_HOME 환경변수 등록

```shell
$ vi /etc/profile
```

* 추가

```text
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-17.0.6.0.10-3.el9.x86_64
```

### 4) profile 적용

```shell
$ source /etc/profile
```

### 5) JAVA_HOME 확인

```shell
$ echo $JAVA_HOME
/usr/lib/jvm/java-17-openjdk-17.0.6.0.10-3.el9.x86_64
```

```shell
$ $JAVA_HOME/bin/javac -version
javac 17.0.6
```

## [출처 및 참고]
* [https://dejavuhyo.github.io/posts/install-openjdk-on-centos/](https://dejavuhyo.github.io/posts/install-openjdk-on-centos/)
