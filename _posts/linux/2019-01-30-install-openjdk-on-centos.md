---
title: CentOS 7에 OpenJDK 설치 및 환경변수 설정
author: dejavuhyo
date: 2019-01-30 17:45:00 +0900
categories: [DevOps, Linux]
tags: [centos-openjdk-install, openjdk-install, centos-java-home, centos-openjdk-설치, openjdk-설치, java-환경변수]
---

## 1. 설치환경

* CentOS 7 VMWare

* OpenJDK 1.8

## 2. 설치된 JDK 확인

```shell
[root@localhost ~]# rpm -qa | grep jdk
```

## 3. 설치된 JDK 삭제

```shell
[root@localhost ~]# yum remove java*
```

## 4. 설치 가능한 JDK 버전 확인

```shell
[root@localhost ~]# yum list java*jdk-devel
```

## 5. 설치

```shell
[root@localhost ~]# yum install java-1.8.0-openjdk-devel.x86_64
```

## 6. 설치 확인

```shell
[root@localhost ~]# javac -version
```

```shell
[root@localhost ~]# rpm -qa java*jdk-devel
```

## 7. JAVA_HOME 설정 확인

* 설정되어 있지 않음

```shell
[root@localhost ~]# echo $JAVA_HOME
```

## 8. javac 위치 확인

### 1) javac 명령어의 위치 확인

```shell
[root@localhost ~]# which javac
```

## 2) 원본 파일 위치 확인
`/usr/bin/javac`는 심볼릭 링크 이므로, 원본 파일 위치 확인

```shell
[root@localhost ~]# readlink -f /usr/bin/javac
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64/bin/javac
```

## 9. JAVA_HOME 설정

### 1) 환경변수 등록
javac의 원본파일 경로를 이용하여 `JAVA_HOME` 환경변수 등록

```shell
[root@localhost ~]# vi /etc/profile
```

### 2) profile 파일 편집
환경변수를 설정하는 profile 파일 편집

```shell
[root@localhost ~]# vi /etc/profile
```

* 제일 아랫줄에 아래 내용 추가

```text
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64
```

### 3) profile 적용

```shell
[root@localhost ~]# source /etc/profile
```

## 10. JAVA_HOME 확인

```shell
[root@localhost ~]# echo $JAVA_HOME
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64
```

```shell
[root@localhost ~]# $JAVA_HOME/bin/javac -version
javac 1.8.0_191
```

## [출처 및 참고]
* <https://blog.hanumoka.net/2018/04/30/centOs-20180430-centos-install-jdk/>
