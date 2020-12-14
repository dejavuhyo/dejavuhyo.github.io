--- 
title: Chrony를 이용한 시간 동기화
author: Hyosik
date: 2020-12-14 10:00:00 +0900
categories: [OS, Linux]
tags: [chrony, ntp, chrony-time-synchronizing, time-synchronizing, 시간-동기화]
---

## 1. 개요
chrony는 RHEL 7에서 기본으로 제공하는 NTP daemon/client 이다. RHEL 7 부터 NTP 대신에 chrony를 제공하고 있다.

chorny가 NTP보다 설정이 더 간결하고 NTP의 단점을 개선하고자 시작된 project 이기 때문에 chrony 사용을 권장 한다.

Time server 설정 전에 우선 알아야 할 것이 Stratum 이라는 의미이다. Stratum은 지층이라는 의미로, NTP protocol은 피라미드 형식의 구성으로 이루어져 있기 때문에, Stratum 0은 피라미드의 꼭대기라고 비유할 수 있다.

Stratum 0은 primary reference clock 이라고 부르며, NTP protocol과는 상관이 없다. 즉 직접적으로 시간 서비스를 하는 것은 아니며, Stratum 1로 시간을 전송하는 장비들을 말하며 primary reference clock 장비에는 GPS, 세슘 원자 시계 등이 있다.

보통 Stratum 1 level의 서버들은 primary reference clock에서 시간을 동기화 하여 서비스를 하며, NTP에서 최상위층이라고 생각하면 된다.

> Cent 8/RHEL 8 버전부터는 NTP가 사라지고 Chrony를 제공하고 있다.

```shell
[root@localhost ~]# dnf install ntp
CentOS-8 - AppStream                                                                                0.0  B/s |   0  B     00:00    
Errors during downloading metadata for repository 'AppStream':
  - Curl error (6): Couldn't resolve host name for http://mirrorlist.centos.org/?release=8&arch=x86_64&repo=AppStream&infra=stock [Could not resolve host: mirrorlist.centos.org]
오류: Failed to download metadata for repo 'AppStream': Cannot prepare internal mirrorlist: Curl error (6): Couldn't resolve host name for http://mirrorlist.centos.org/?release=8&arch=x86_64&repo=AppStream&infra=stock [Could not resolve host: mirrorlist.centos.org]
```

## 2. 설치
chrony는 NTP와 동일하게 Time service를 하지 않고 서버의 시간 동기화를 하기만 하려고 해도 기본적으로 daemon으로 구동을 해야 한다.

### 1) ntp package 삭제

```shell
[root@localhost ~]# dnf remove ntp
No match for argument: ntp
제거 할 수있는 패키지가 없습니다.
Dependencies resolved.
할 것이 없음.
완료되었습니다!
```

### 2) chrony 설치

```shell
[root@localhost ~]# dnf install chrony
CentOS-8 - AppStream                                                                              6.4 MB/s | 6.2 MB     00:00    
CentOS-8 - Base                                                                                   4.0 MB/s | 2.3 MB     00:00    
CentOS-8 - Extras                                                                                  12 kB/s | 8.1 kB     00:00    
Package chrony-3.5-1.el8.x86_64 is already installed.
Dependencies resolved.
할 것이 없음.
완료되었습니다!
```

## 3. 방화벽 등록

```shell
[root@localhost ~]# firewall-cmd --add-service=ntp --permanent
[root@localhost ~]# firewall-cmd l--reload
```

## 4. 시작 및 자동 실행 설정

```shell
[root@localhost ~]# sudo systemctl start chronyd
[root@localhost ~]# sudo systemctl enable chronyd
[root@localhost ~]# sudo systemctl restart chronyd
```

## 5. 설정

### 1) 타임서버 설정
기본으로 설정이 되어 있는 CentOS NTP pool의 time server가 아니라, 다른 Time server에서 동기화를 하고 싶다면 /etc/chrony.conf 에서 server 지시자에 원하는 Time server를 등록하면 된다.

```shell
[root@localhost ~]# vi /etc/chrony.conf
```

```text
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
# pool 2.centos.pool.ntp.org iburst  
  
# 한국 공용 타임서버
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst

# Allow NTP client access from local network.
# 내부 네트워크에서 이 서버를 타임 서버로 참조하기 위한 설정
#allow 192.168.0.0/16
```

### 2) 동기화

* 상태 확인

```shell
[root@localhost ~]# timedatectl status
               Local time: 일 2020-12-13 21:55:32 EST
           Universal time: 월 2020-12-14 02:55:32 UTC
                 RTC time: 월 2020-12-14 02:55:32
                Time zone: America/New_York (EST, -0500)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

* System clock synchronized: no 일경우

```shell
[root@localhost ~]# timedatectl set-ntp yes
```

### 3) 시간대 설정

* 동기화 실행

```shell
[root@localhost ~]# timedatectl set-ntp true
```

* 시간대 설정

```shell
[root@localhost ~]# timedatectl set-timezone Asia/Seoul
```

* 지역별 시간대 확인

```shell
[root@localhost ~]# tzselect
Please identify a location so that time zone rules can be set correctly.
Please select a continent, ocean, "coord", or "TZ".
 1) Africa
 2) Americas
 3) Antarctica
 4) Asia
 5) Atlantic Ocean
 6) Australia
 7) Europe
 8) Indian Ocean
 9) Pacific Ocean
10) coord - I want to use geographical coordinates.
11) TZ - I want to specify the time zone using the Posix TZ format.
#? 4
Please select a country whose clocks agree with yours.
 1) Afghanistan		  18) Israel		    35) Palestine
 2) Armenia		  19) Japan		    36) Philippines
 3) Azerbaijan		  20) Jordan		    37) Qatar
 4) Bahrain		  21) Kazakhstan	    38) Russia
 5) Bangladesh		  22) Korea (North)	    39) Saudi Arabia
 6) Bhutan		  23) Korea (South)	    40) Singapore
 7) Brunei		  24) Kuwait		    41) Sri Lanka
 8) Cambodia		  25) Kyrgyzstan	    42) Syria
 9) China		  26) Laos		    43) Taiwan
10) Cyprus		  27) Lebanon		    44) Tajikistan
11) East Timor		  28) Macau		    45) Thailand
12) Georgia		  29) Malaysia		    46) Turkmenistan
13) Hong Kong		  30) Mongolia		    47) United Arab Emirates
14) India		  31) Myanmar (Burma)	    48) Uzbekistan
15) Indonesia		  32) Nepal		    49) Vietnam
16) Iran		  33) Oman		    50) Yemen
17) Iraq		  34) Pakistan
#? 23

The following information has been given:

	Korea (South)

Therefore TZ='Asia/Seoul' will be used.
Selected time is now:	Mon Dec 14 12:59:44 KST 2020.
Universal Time is now:	Mon Dec 14 03:59:44 UTC 2020.
Is the above information OK?
1) Yes
2) No
#? 1

You can make this change permanent for yourself by appending the line
	TZ='Asia/Seoul'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /usr/bin/tzselect command in shell scripts:
Asia/Seoul
```

* 최종 설정 확인

```shell
[root@localhost ~]# timedatectl status
               Local time: 월 2020-12-14 12:00:47 KST
           Universal time: 월 2020-12-14 03:00:47 UTC
                 RTC time: 월 2020-12-14 03:00:47
                Time zone: Asia/Seoul (KST, +0900)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

## [출처 및 참고]
* <https://joungkyun.gitbook.io/annyung-3-user-guide/chapter6/chapter6-chrony>
* <https://itragdoll.tistory.com/23>
* <https://m.blog.naver.com/PostView.nhn?blogId=hymne&logNo=221007971952&proxyReferer=https:%2F%2Fwww.google.com%2F>
