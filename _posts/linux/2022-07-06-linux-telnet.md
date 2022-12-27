---
title: Linux telnet
author: dejavuhyo
date: 2022-07-06 11:30:00 +0900
categories: [DevOps, Linux]
tags: [linux-telnet, telnet, 리눅스-telnet, 리눅스]
---

## 1. telnet
네트워크 호스트와 포트에 연결이 가능한지 확인할 수 있는 기본 명령어 이다.

## 2. 사용예제

```shell
# 192.168.1.20주소의 80번 포트에 접속
$ telnet 192.168.1.20 80

# host-1에 연결. 연결 성공
$ telnet host-1 8050
Trying 192.168.0.1...
Connected to host-1
Escape character is '^]'.

# host-2에 연결. 연결 실패
# telnet host-2 8050
Trying 10.202.52.63...
telnet: connect to address 192.168.0.2: Connection refused
```

## [출처 및 참고]
* <https://wikidocs.net/92663>
