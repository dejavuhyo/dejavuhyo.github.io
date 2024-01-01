---
title: Linux 계정 변경 sudo, su
author: dejavuhyo
date: 2022-07-20 23:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-sudo, linux-su, sudo, su, 리눅스-sudo, 리눅스-su, 리눅스-계정-변경]
---

## 1. sudo, su
리눅스에서 현재 접속한 계정과 다른 계정에서 작업을 처리해야 할 때 `su`, `sudo`를 이용해 처리할 수 있다.

## 2. su
`su`는 substitute user의 약어이다. root 계정이나 다른 계정으로 변경할 때 사용한다.

```java
# su를 입력하면 root 계정으로 변경. 암호가 필요함
$ su
Password:

# root 계정으로 변경하면서, root의 환경변수를 이용
$ su -
Password:

# hdfs 계정으로 변경
$ su hdfs
$ su - hdfs
```

## 3. sudo
`sudo`는 substitute user do의 약어로 변경된 계정으로 명령어만 실행하는 명령어이다. 리눅스 머신에 명령어를 설치할 때 많이 사용된다.

```java
# root 계정으로 jq 명령어 설치
$ sudo yum install jq

# hdfs 계정으로 hdfs dfsadmin -report 명령어를 실행
$ sudo -u hdfs hdfs dfsadmin -report
```

## [출처 및 참고]
* [https://wikidocs.net/72730](https://wikidocs.net/72730)
