---
title: 리눅스 logrotate
author: dejavuhyo
date: 2022-02-15 10:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-logrotate, logrotate, 리눅스-logrotate]
---

## 1. logrotate
시스템을 운영하다 보면 로그가 쌓이게 된다. 이 로그를 정리할 수 있는 명령어가 logrotate이다.

이 명령어는 데몬이 아니라서 크론탭을 이용하여 주기적으로 로그를 정리하는 명령을 추가해야 한다.

## 2. 주요 옵션

| 옵션 | 내용 |
|:-----:|:-----:|
| -s | 상태 파일 위치 |
| -f | 로그 포맷 옵션 지정 |

## 3. 로그 포맷
로그를 정리 하는 포맷은 다음과 같다. 정리할 로그의 위치를 지정하고 중괄호를 이용하여 로그를 저장한다.

* daily, weekly, monthly, yearly: 일별로 처리

* rotate: 정리 주기

* dateext: 일자로 파일 정리

* compress: 압축

* notifempty: 파일사이즈가 0이면 처리 안함

* create: 정리한 로그 파일의 권한 설정

* copytruncate: 로그를 정리하고 파일 사이즈를 0으로 변경

* olddir: 정리한 로그를 저장할 위치

```text
/home/deploy/tomcat/logs/weblog.log
{
    daily
    dateext
    compress
    rotate 14
    notifempty
    create 644 deploy deploy
    copytruncate
    olddir /home/deploy/tmocat/backup
}
```

## 4. 사용예제

```shell
# 실행
logrotate -s /home/deploy/logstatus -f /home/deploy/logconf.conf

# 실행 후
# 다음과 같이 로그 처리 압축파일이 생성됨
total 6000
-rw-r--r-- 1 deploy deploy    4350 Dec  3 17:51 airflow-scheduler.err
-rw-r--r-- 1 deploy deploy     480 Dec  1 11:35 airflow-scheduler.err-20211201.gz
-rw-r--r-- 1 deploy deploy     457 Dec  2 01:00 airflow-scheduler.err-20211202.gz
-rw-r--r-- 1 deploy deploy   97415 Dec  3 22:58 airflow-scheduler.log
-rw-r--r-- 1 deploy deploy  235222 Dec  1 12:27 airflow-scheduler.log-20211201.gz
-rw-r--r-- 1 deploy deploy    6187 Dec  1 23:57 airflow-scheduler.log-20211202.gz
-rw-r--r-- 1 deploy deploy  142176 Dec  3 22:58 airflow-scheduler.out
-rw-r--r-- 1 deploy deploy    8345 Dec  1 12:27 airflow-scheduler.out-20211201.gz
-rw-r--r-- 1 deploy deploy    6875 Dec  1 23:57 airflow-scheduler.out-20211202.gz
```

## [출처 및 참고]
* <https://wikidocs.net/155602>
