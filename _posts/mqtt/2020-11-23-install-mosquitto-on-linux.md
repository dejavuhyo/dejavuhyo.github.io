---
title: 리눅스에 Mosquitto 설치
author: Hyosik
date: 2020-11-23 09:30:00 +0900
categories: [Tools, MQTT]
tags: [mosquitto_install, mosquitto_linux, mosquitto_install_linux]
---

## 1. 계정 추가

```bash
[root@localhost ~]# useradd mosquitto
[root@localhost ~]# groupadd mosquitto
```

## 2. 의존성 패키지 설치

```bash
[root@localhost ~]# yum install gcc*
[root@localhost ~]# yum install openssl openssl-devel
[root@localhost ~]# yum install pcre pcre-devel
[root@localhost ~]# yum install zlib zlib-devel
[root@localhost ~]# yum install glibc glibc-devel
```


## 출처 및 참고
* <http://cheonbrave.blogspot.com/2016/11/mqtt-mosquitto-broker.html>
