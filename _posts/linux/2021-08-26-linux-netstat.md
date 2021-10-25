---
title: Linux 네트워크 상태 확인
author: dejavuhyo
date: 2021-08-26 06:00:00 +0900
categories: [DevOps, Linux]
tags: [linus-netstat, netstat, 리눅스-netstat, 리눅스-네트워크-상태, 네트워크-상태]
---

## 1. netstat란
네트워크 접속, 라우팅 테이블, 네트워크 인터페이스의 통계 정보를 보여주는 도구이다.

## 2. 사용방법

```bash
$ netstat [옵션] [| grep 포트 번호 or 서비스 명]
```

## 3. 옵션

```text
-t (tcp): tcp 프로토콜
-u (udp): udp 프로토콜
-n (numeric): 호스트명이 아닌 IP주소로 표시
-p (programs): PID/프로그램 이름
-a (all): 모든 소켓 (기본은 연결상태인 것만 나온다.)
-l (lintening): 연결 가능한 상태
-r (route): 라우팅 테이블
-i (interfaces): 인터페이스 테이블 (이더넷 카드별 정상/에러/드랍 송수신 패킷 수 확인)
-s (statistics): 네트워크 통계
-v (verbose): 상세내용
```

### 1) 자주 사용하는 옵션

* 연결을 기다리는 목록과 프로그램 보기

```bash
$ netstat -nap
```

* 특정 포트가 사용 중인지 확인

```bash
$ netstat -an | grep 포트번호
```

* TCP listening 상태의 포트와 프로그램 보기

```bash
$ netstat -nlpt
```

## 4. netstat 상태값

| State | Description |
|:---:|:---:|
| CLOSED | 완전히 연결이 종료된 상태 |
| CLOSING | 흔하지 않으나 주로 확인 메시지가 전송 도중 유실된 상태 |
| CLOSE_WAIT | TCP 연결이 상위 응용프로그램 레벨로부터 연결 종료를 기다리는 상태 |
| ESTABLISHED | 서버와 클라이언트 간에 세션 연결이 성립되어 통신이 이루어지고 있는 상태 (클라이언트가 서버의 SYN을 받아서 세션이 연결된 상태) |
| FIN_WAIT1 | 클라이언트가 서버에게 연결을 끊고자 요청하는 상태 (FIN을 보낸 상태) |
| FIN_WAIT2 | 서버가 클라이언트로부터 연결 종료 응답을 기다리는 상태 (서버가 클라이언트로부터 최초로 FIN을 받은 후, 클라이언트에게 ACK를 주었을 때 |
| LAST_ACK | 호스트가 원격지 호스트의 연결 종료 요구 승인을 기다리는 상태 (서버가 클라이언트에게 FIN을 보냈을 때의 상태) |
| LISTEN | 서버의 데몬이 떠 있어서 클라이언트의 접속 요청을 기다리고 있는 상태 |
| SYN_SENT | 클라이언트가 서버에게 연결을 요청한 상태 |
| SYN_RECEIVED | 서버가 클라이언트로부터 접속 요구(SYN)을 받아 클라이언트에게 응답(SYN/ACK)하였지만, 아직 클라이언트에게 확인 메시지(ACK)는 받지 못한 상태 |
| TIME_WAIT | 연결은 종결되었지만 당분간 소켓을 열어 놓은 상태, 약 1분 정도이며 시간이 지나면 사라짐 |
| UNKNOWN | 소켓의 상태를 알 수 없음 |

## [출처 및 참고]
* <https://baejangho.com/entry/Linux-how-to-see-network-stat>
* <https://m.blog.naver.com/PostView.nhn?blogId=ncloud24&logNo=221388026417&proxyReferer=https:%2F%2Fwww.google.com%2F>
