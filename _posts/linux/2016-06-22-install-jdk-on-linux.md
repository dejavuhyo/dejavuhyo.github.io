---
title: 리눅스 JDK 설치
author: dejavuhyo
date: 2016-06-22 18:18:00 +0900
categories: [DevOps, Linux]
tags: [install-jdk-linux, install-java-linux, linux-jdk, linux-java, 리눅스-jdk-설치, 리눅스-java-설치, jdk-설치, java-설치]
---

## 1. 설치 파일

* jdk-6u45-linux-x64.bin

## 2. JDK 다운로드
* <http://www.oracle.com/technetwork/java/javase/archive-139210.html>

## 3. 압축 풀기

```shell
$ sh jdk-6u45-linux-x64.bin
```

## 4. 환경변수 설정
root 계정 필요하고, 환경변수 설정은 `/etc/profile` 파일에 작성 한다.

### 1) vi를 통해 profile 열기

```shell
$ vi /etc/profile
```

### 2) 아래쪽에 작성

```text
export JAVA_HOME=/home/jiran/jdk1.6.0_45
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH="."
```
