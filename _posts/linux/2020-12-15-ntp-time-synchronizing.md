--- 
title: NTP를 이용한 시간 동기화
author: dejavuhyo
date: 2020-12-15 09:10:00 +0900
categories: [OS, Linux]
tags: [ntp, chrony-time-synchronizing, time-synchronizing, 시간-동기화, ntp-시간-동기화]
---

## 1. 개요
Time server 설정 전에 우선 알아야 할 것이 Stratum 이라는 의미이다.

Stratum은 지층이라는 의미로, NTP protocol은 피라미드 형식의 구성으로 이루어져 있기 때문에, Stratum 0은 피라미드의 꼭대기라고 비유할 수 있다.

Stratum 0은 primary reference clock 이라고 부르며, NTP protocol과는 상관이 없다.

즉, 직접적으로 시간 서비스를 하는 것은 아니며, Stratum 1로 시간을 전송하는 장비들을 말하며 primary reference clock 장비에는 GPS, 세슘 원자 시계 등이 있다.

보통 Stratum 1 level의 서버들은 primary reference clock에서 시간을 동기화 하여 서비스를 하며, NTP에서 최상위층이라고 생각하면 된다.

다만, Stratum 1 level의 서버들은 client들이 Stratum 1 서버에서 동기화를 하면 시간이 더욱 정확할 것이라는 생각으로 Stratum 1 서버들을 설정하여 서비스 부하가 높아져서 Stratum 2 서버들에만 접근을 허가하고 access를 막아 놓은 경우가 대부분 이다.

또한, NTP 구성 목적이 대부분 정확한 시간 보다는 시간의 동기화에 있기 때문에 꼭 최상위 stratum에 동기화를 할 이유가 별로 없기 때문에 Stratum 2 정도에 sync를 하는 것을 권장 한다.

> Cent 8/RHEL 8 버전부터는 NTP가 사라지고 Chrony를 제공하고 있다.

## 2. 설치
NTP는 Time Service를 하지 않고 서버의 시간 동기화를 하기만 하려고 해도 기본적으로 Daemon으로 구동을 해야 한다.

### 1) Chrony Package 삭제

```shell
[root@localhost ~]# yum remove chrony
```

### 2) NTP 설치

```shell
[root@localhost ~]# yum install ntp
```

## 3. 설정

### 1) 타임서버 설정

```shell
[root@localhost ~]# vi /etc/ntp.conf
```

```text
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst

server 0.asia.pool.ntp.org
server 1.asia.pool.ntp.org
server 2.asia.pool.ntp.org
server 3.asia.pool.ntp.org
```

## 4. 방화벽 등록

```shell
[root@localhost ~]# firewall-cmd --add-service=ntp --permanent
[root@localhost ~]# firewall-cmd --reload
```

## 5. 시작 및 자동 실행 설정

```shell
[root@localhost ~]# systemctl start ntpd
[root@localhost ~]# systemctl enable ntpd
[root@localhost ~]# systemctl restart ntpd
```

## 6. 동기화 확인

* 동기화까지 약15분 정도 시간이 소요된다.

```shell
[root@localhost ~]# ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
*tama.paina.net  122.103.125.181  2 u    7   64   77   39.117   18.858   1.971
+203.107.6.88    100.107.25.114   2 u    3   64   77   75.165   12.033  11.261
+119.28.183.184  100.122.36.196   2 u   64   64   37   45.685    9.284   3.194
-time.cloudflare 10.51.8.120      3 u    2   64   77   25.055    0.627   1.882
```

* *: 현재 sync를 받고 있음

* +: ntp 알고리즘에 의해 접속은 가능하지만 sync 를 하고 있지는 않음

* -: ntp 알고리즘에 의해 접속은 가능하지만 sync 가능 리스트에서 제외

* blank: 접속 불가능

## [출처 및 참고]
* <https://webdir.tistory.com/120>
* <https://www.manualfactory.net/10147>
