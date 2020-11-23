---
title: 리눅스에 Mosquitto 설치
author: Hyosik
date: 2020-11-22 09:30:00 +0900
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

## 3. OpenSSL 설치

### 1) 다운로드

```bash
[root@localhost ~]# wget http://www.openssl.org/source/openssl-1.1.1g.tar.gz
[root@localhost ~]# tar -zxvf openssl-1.1.1g.tar.gz
[root@localhost ~]# cp -Rf openssl-1.1.1g /usr/local/openssl
[root@localhost ~]# cd /usr/local/openssl

[root@localhost openssl]# export LDFLAGS=-L/usr/local/lib
[root@localhost openssl]# export CPPFLAGS=-I/usr/local/include
[root@localhost openssl]# make clean
[root@localhost openssl]# ./config --prefix=/usr --openssldir=/usr/local/openssl shared threads zlib
```

### 2) 설치

```bash
[root@localhost openssl]# make
[root@localhost openssl]# make install
[root@localhost openssl]# make clean
[root@localhost openssl]# unset LDFLAGS
[root@localhost openssl]# unset CPPFLAGS
```

### 3) 라이브러리 로딩 추가

* 편집
```bash
[root@localhost ~]# vi /etc/ld.so.conf
```
* 추가

include ld.so.conf.d/*.conf
include /usr/local/openssl/lib

* 적용

```
[root@localhost ~]# /sbin/ldconfig
```

### 4) 설치 확인

```bash
[root@localhost ~]# openssl version
```

## 4. Mosquitto 설치

### 1) 다운로드

```bash
[root@localhost ~]# wget http://mosquitto.org/files/source/mosquitto-1.6.12.tar.gz
[root@localhost ~]# tar -zxvf mosquitto-1.6.12.tar.gz
[root@localhost ~]# cp -Rf mosquitto-1.6.12 /usr/local/mosquitto
[root@localhost ~]# cd /usr/local/mosquitto
```

### 2) 설치

```bash
[root@localhost mosquitto]# make
[root@localhost mosquitto]# make install
```

### 3) 라이브러리 로딩 추가

* 편집

```bash
[root@localhost ~]# vi /etc/ld.so.conf
```

* 추가

include /usr/local/mosquitto/lib

# 적용

```bash
[root@localhost ~]# /sbin/ldconfig
```

* ld.so.conf 내용

```text
include ld.so.conf.d/*.conf
include /usr/local/openssl/lib
include /usr/local/mosquitto/lib
/usr/lib
/usr/local/lib
```

### 4) 심볼릭 링크 생성

```bash
ln -s /usr/local/mosquitto/lib/libmosquitto.so.1 /usr/lib/libmosquitto.so.1
```

### 5) MOSQUITTO_HOME 설정

* 편집

```bash
[root@localhost ~]# vi /etc/profile
```

* 추가
MOSQUITTO_HOME=/usr/local/mosquitto
export MOSQUITTO_HOME
PATH=$PATH:$MOSQUITTO_HOME/src
export PATH

* 적용

```bash
[root@localhost ~]# source /etc/profile
```

### 6) Port 열기

```bash
[root@localhost ~]# iptables -I INPUT -m tcp -p tcp --dport 1883 -j ACCEPT
[root@localhost ~]# /sbin/service iptables save
[root@localhost ~]# /sbin/service iptables restart
```

### 7) Broker 실행

```bash
[root@localhost mosquitto]# mosquitto
# OR
[root@localhost mosquitto]# mosquitto -c ./mosquitto.conf -d
# OR
[root@localhost mosquitto]# nohup mosquitto -c ./mosquitto.conf -d &
```

## 출처 및 참고
* <http://cheonbrave.blogspot.com/2016/11/mqtt-mosquitto-broker.html>
