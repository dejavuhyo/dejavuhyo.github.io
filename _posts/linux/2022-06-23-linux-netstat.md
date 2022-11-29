---
title: Linux netstat
author: dejavuhyo
date: 2022-06-23 10:20:00 +0900
categories: [DevOps, Linux]
tags: [linux-netstat, netstat, 리눅스-netstat, 리눅스]
---

## 1. netstat
네트워크 인터페이스, 경로 등의 네트워크 정보를 출력한다. 네트워크 접속 정보, 포트 정보 등을 출력한다.

## 2. 주요 옵션

| 옵션 | 설명 |
|:-----:|:-----:|
| `-a` | 경로테이블을 함께 출력한다. |
| `-n` | 네트워크 주소를 숫자(IP 정보)로 출력한다. |
| `-i` | 네트워크 접속 상태를 출력한다. |
| `-o` | 타이머 정보를 출력한다. |
| `-l` | LISTENING 상태의 정보만 출력한다. |
| `-p` | 프로세스 정보를 출력한다. |

## 3. 출력정보

| 상태 | 정보 |
|:-----:|:-----:|
| Proto | 소켓이 사용하는 프로토콜 정보(tcp, udp, raw) |
| Recv-Q | 소켓에서 사용자 프로그램으로 전달되지 않은 바이트 사이즈 |
| Send-Q | 원격지에 전달하지 않은 바이트 사이즈 |
| Local Address | 로컬의 아이피와 포트 번호 |
| Foreign Address | 원격지의 아이피와 포트 번호 |
| State | 연결 상태 |

## 4. 연결상태정보

| 상태 | 정보 |
|:-----:|:-----:|
| ESTABLISHED | 소켓이 연결되었음 |
| SYN_SENT | 소켓이 연결을 시도 중임 |
| SYN_RECV | 네트워크에서 연결 요청을 받았음 |
| FIN_WAIT1 | 소켓이 종료되고, 커넥션을 종료중 |
| FIN_WAIT2 | 커넥션이 종료되고, 소켓이 원격지의 연결 종료를 대기중 |
| TIME_WAIT | 소켓은 네트워크에 여전히 남아있는 패킷을 처리하기 위해 대기중 |
| CLOSED | 소켓이 사용되지 않는중 |
| CLOSE_WAIT | 원격지의 연결이 종료되고, 소켓이 닫히는 것을 대기중 |
| LAST_ACK | 원격지 연결이 종료되고, 소켓이 닫혔음. 승인 대기중 |
| LISTEN | 소켓이 연결을 수신하고 있음. `-l`, `-a` 옵션이 없으면 출력되지 않음 |
| CLOSING | 소켓이 종료되었지만, 데이터가 전송중임 |
| UNKNOWN | 상태를 알 수 없음 |

## 5. 사용예제

```shell
# 네트워크 연결상태, 소켓정보 출력
$ netstat
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address               Foreign Address             State
tcp        0      0 ip--.ap-no:20022            ip--.ap-nort:43464 ESTABLISHED
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node Path
unix  2      [ ]         DGRAM                    7150   @/org/kernel/udev/udevd

# 네트워크 정보를 숫자로 표현
$ netstat -n
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address               Foreign Address             State
tcp        0      0 192.168.0.1:20022           192.168.0.2:43464            ESTABLISHED
ctive UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node Path
unix  2      [ ]         DGRAM                    7150   @/org/kernel/udev/udevd


# 네트워크 인터페이스(랜카드)의 접속 상태
$ netstat -i
Kernel Interface table
Iface       MTU Met    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
eth0       9001   0  1590440      0      0      0  1616447      0      0      0 BMRU
lo        65536   0     6830      0      0      0     6830      0      0      0 LRU


# 모든 상태를 IP 숫자로 표현
$ netstat -an

# state가 ESTABLISHED, TIME_WAIT 인 것 만 출력
$ netstat -an | egrep "ESTABLISHED|TIME_WAIT"
```

## [출처 및 참고]
* <https://wikidocs.net/48021>
