---
title: CentOS 7에 OracleJDK 설치 및 환경변수 설정
author: dejavuhyo
date: 2019-02-19 11:00:00 +0900
categories: [DevOps, Linux]
tags: [centos-oraclejdk-install, oraclejdk-install, centos-java-home, jdk, oraclejdk, java-home, centos-oraclejdk-설치, oraclejdk-설치, java-환경변수]
---

## 1. 설치환경

* CentOS 7 (VMWare)

* jdk-8u201-linux-x64.tar.gz

## 2. 설치된 JDK 확인

```shell
$ rpm -qa | grep jdk
```

## 3. 설치된 JDK 삭제

```shell
yum remove java*
```

## 4. jdk-8u201-linux-x64.tar.gz 다운로드

![img001](/assets/img/2019-02-19-install-oraclejdk-on-centos/img001.png)

## 5. 압축 풀기

* `/usr/local` 경로

```shell
$ tar -zxvf jdk-8u201-linux-x64.tar.gz
```

## 6. 바로가기 설정
jdk 버전 변경시 `/etc/profile` 수정 없이 바로가기만 변경하면 되기 때문에 편리함

```shell
$ ln -s jdk1.8.0_201 java
```

![img002](/assets/img/2019-02-19-install-oraclejdk-on-centos/img002.png)

## 7. 환경변수 설정

```shell
$ vi /etc/profile
```

아래쪽에 작성

```text
export JAVA_HOME=/usr/local/java
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/tools.jar
```

적용 (혹은 reboot)

```shell
$ source /etc/profile
```

## 8. 설치 확인

```shell
[root@localhost ~]# java -version
java version "1.8.0_201"
Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.201-b09, mixed mode)
```
