---
title: Linux ifconfig
author: dejavuhyo
date: 2022-06-21 14:10:00 +0900
categories: [DevOps, Linux]
tags: [linux-ifconfig, ifconfig, 리눅스-ifconfig, 리눅스]
---

## 1. ifconfig
네트워크 인터페이스를 확인하고 설정한다.

## 2. 사용예제

```shell
# 네트워크 인터페이스의 정보 확인
$ ifconfig
eth0      Link encap:Ethernet  HWaddr 02:DA:18:B7:C6:F7
          inet addr:192.168.0.3  Bcast:192.168.0.255  Mask:255.255.255.0
          inet6 addr: Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:9001  Metric:1
          RX packets:1629317 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1649688 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:472859354 (450.9 MiB)  TX bytes:238864810 (227.7 MiB)
          Interrupt:155

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:6834 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6834 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:8946461 (8.5 MiB)  TX bytes:8946461 (8.5 MiB)

# 네트워크 인터페이스 설정 - IP 주소 설정
$ ifconfig eth0 192.168.0.1 broadcast 192.168.0.255 netmask 255.255.255.0
```

## [출처 및 참고]
* <https://wikidocs.net/48057>
