---
title: Linux에 Mosquitto 설치
author: dejavuhyo
date: 2020-11-23 10:00:00 +0900
categories: [DevOps, MQTT]
tags: [mosquitto-install, mosquitto-linux, mosquitto-install-linux]
---

## 1. 계정 추가

```shell
[root@localhost ~]# useradd mosquitto
[root@localhost ~]# groupadd mosquitto
```

## 2. 의존성 패키지 설치

```shell
[root@localhost openssl]# make
[root@localhost openssl]# make install
[root@localhost openssl]# make clean
[root@localhost openssl]# unset LDFLAGS
[root@localhost openssl]# unset CPPFLAGS
```

## 3. OpenSSL 설치

### 1) 다운로드

```shell
[root@localhost ~]# wget https://www.openssl.org/source/openssl-1.1.1g.tar.gz
[root@localhost ~]# tar -zxvf openssl-1.1.1g.tar.gz
[root@localhost ~]# cp -Rf openssl-1.1.1g /usr/local/openssl
[root@localhost ~]# cd /usr/local/openssl
 
[root@localhost openssl]# export LDFLAGS=-L/usr/local/lib
[root@localhost openssl]# export CPPFLAGS=-I/usr/local/include
[root@localhost openssl]# make clean
[root@localhost openssl]# ./config --prefix=/usr --openssldir=/usr/local/openssl shared threads zlib
```

### 2) 설치

```shell
[root@localhost openssl]# make
[root@localhost openssl]# make install
[root@localhost openssl]# make clean
[root@localhost openssl]# unset LDFLAGS
[root@localhost openssl]# unset CPPFLAGS
```

### 3) 라이브러리 로딩 추가

* 편집

```shell
[root@localhost ~]# vi /etc/ld.so.conf
```

* 추가

```text
include ld.so.conf.d/*.conf
include /usr/local/openssl/lib
```

* 적용

```shell
[root@localhost ~]# /sbin/ldconfig
```

### 4) 설치 확인

```shell
[root@localhost ~]# openssl version
```

## 4. Mosquitto 설치

### 1) 다운로드

```shell
[root@localhost ~]# wget https://mosquitto.org/files/source/mosquitto-1.6.12.tar.gz
[root@localhost ~]# tar -zxvf mosquitto-1.6.12.tar.gz
[root@localhost ~]# cp -Rf mosquitto-1.6.12 /usr/local/mosquitto
[root@localhost ~]# cd /usr/local/mosquitto
```

### 2) 설치

```shell
[root@localhost mosquitto]# make
[root@localhost mosquitto]# make install
```

### 3) 라이브러리 로딩 추가

* 편집

```shell
[root@localhost ~]# vi /etc/ld.so.conf
```

* 추가

```text
include /usr/local/mosquitto/lib
```

* 적용

```shell
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

```shell
[root@localhost ~]# ln -s /usr/local/mosquitto/lib/libmosquitto.so.1 /usr/lib/libmosquitto.so.1
```

### 5) MOSQUITTO_HOME 설정

* 편집

```shell
[root@localhost ~]# vi /etc/profile
```

* 추가

```text
MOSQUITTO_HOME=/usr/local/mosquitto
export MOSQUITTO_HOME
PATH=$PATH:$MOSQUITTO_HOME/src
export PATH
```

* 적용

```shell
[root@localhost ~]# source /etc/profile
```

### 6) Port 열기

```shell
[root@localhost ~]# iptables -I INPUT -m tcp -p tcp --dport 1883 -j ACCEPT
[root@localhost ~]# /sbin/service iptables save
[root@localhost ~]# /sbin/service iptables restart
```

### 7) Broker 실행

```shell
[root@localhost mosquitto]# mosquitto
# OR
[root@localhost mosquitto]# mosquitto -c ./mosquitto.conf -v
```

## [출처 및 참고]
* [https://cheonbrave.blogspot.com/2016/11/mqtt-mosquitto-broker.html](https://cheonbrave.blogspot.com/2016/11/mqtt-mosquitto-broker.html)
