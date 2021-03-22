---
title: TCP/UDP 포트 목록
author: dejavuhyo
date: 2021-03-04 06:10:00 +0900
categories: [OS, Linux]
tags: [tcp-udp-port, tcp-port, udp-port, tcp-포트, udp-포트]
---

## 1. TCP/UDP 포트
알려진 포트(well-known port)는 특정한 쓰임새를 위해서 IANA에서 할당한 TCP 및 UDP 포트 번호의 일부이다. 일반적으로 포트 번호는 다음과 같이 세 가지로 나눌 수 있다.

* 0번 ~ 1023번: 잘 알려진 포트 (well-known port)
* 1024번 ~ 49151번: 등록된 포트 (registered port)
* 49152번 ~ 65535번: 동적 포트 (dynamic port)

## 2. 잘 알려진 포트
대부분의 유닉스 계열 운영 체제의 경우, 잘 알려진 포트를 열려면 root 권한이 있어야 한다.

이 번호는 강제적으로 지정된 것은 아니며, IANA의 권고안일 뿐이다. 가끔 각 포트 번호를 그대로 사용하지 않고 다른 용도로 사용하기도 한다. 이 경우, 트로이 목마와 같은 프로그램들이 악의적인 목적으로 포트를 변경하여 사용하는 경우도 있다.

| 포트 | TCP | UDP | 설명 | 상태 |
|:---:|:---:|:---:|:---:|:---:|
| 0 |  | UDP | 예약됨; 사용하지 않음 | 공식 |
| 1 | TCP |  | TCPMUX (TCP 포트 서비스 멀티플렉서) | 공식 |
| 7 | TCP | UDP | ECHO 프로토콜 | 공식 |
| 9 | TCP | UDP | DISCARD 프로토콜 | 공식 |
| 13 | TCP | UDP | DAYTIME 프로토콜 | 공식 |
| 17 | TCP |  | QOTD (Quote of the Day) 프로토콜 | 공식 |
| 19 | TCP | UDP | CHARGEN (Character Generator) 프로토콜 - 원격 오류 수정 | 공식 |
| 20 | TCP |  | 파일 전송 프로토콜 (FTP, File Transfer Protocol) - 데이터 포트 | 공식 |
| 21 | TCP |  | 파일 전송 프로토콜 (FTP, File Transfer Protocol) - 제어 포트 | 공식 |
| 22 | TCP |  | 시큐어 셸 (SSH, Secure SHell) - ssh scp, sftp같은 프로토콜 및 포트 포워딩 | 공식 |
| 23 | TCP |  | 텔넷 프로토콜 (Telnet Protocol) - 암호화되지 않은 텍스트 통신 | 공식 |
| 24 | TCP |  | 개인메일 시스템 | 공식 |
| 25 | TCP |  | SMTP (Simple Mail Transfer Protocol) - 이메일 전송에 사용 | 공식 |
| 37 | TCP | UDP | TIME 프로토콜 | 공식 |
| 49 |  | UDP | TACACS 프로토콜 | 공식 |
| 53 | TCP | UDP | 도메인 네임 시스템 (DNS, Domain Name System) | 공식 |
| 67 |  | UDP | BOOTP (부트스트랩 프로토콜) 서버. DHCP로도 사용 | 공식 |
| 68 |  | UDP | BOOTP (부트스트랩 프로토콜) 클라이언트. DHCP로도 사용 | 공식 |
| 69 |  | UDP | 간단한 파일 전송 프로토콜 (TFTP, Trivial File Transfer Protocol) | 공식 |
| 70 | TCP |  | 고퍼 프로토콜 (Gopher Protocol) | 공식 |
| 79 | TCP |  | Finger 프로토콜 | 공식 |
| 80 | TCP | UDP | HTTP (HyperText Transfer Protocol) - 웹 페이지 전송 | 공식 |
| 88 | TCP |  | 커베로스 - 인증 에이전트 | 공식 |
| 109 | TCP |  | POP2 (Post Office Protocol version 2) - 전자우편 가져오기에 사용 | 공식 |
| 110 | TCP |  | POP3 (Post Office Protocol version 3) - 전자우편 가져오기에 사용 | 공식 |
| 111 | TCP | UDP | RPC (Remote Procedure Call) | 공식 |
| 113 | TCP |  | ident - 예전 서버 인증 시스템, 현재는 IRC 서버에서 사용자 인증에 사용 | 공식 |
| 119 | TCP |  | NNTP (Network News Transfer Protocol) - 뉴스 그룹 메시지 가져오기에 사용 | 공식 |
| 123 |  | UDP | NTP (Network Time Protocol) - 시간 동기화 | 공식 |
| 139 | TCP |  | 넷바이오스 (NetBIOS, Network Basic Input/Output System) | 공식 |
| 143 | TCP |  | 인터넷 메시지 접속 프로토콜 4 (IMAP4, Internet Message Access Protocol 4) - 이메일 가져오기에 사용 | 공식 |
| 161 |  | UDP | SNMP (Simple Network Management Protocol) - Agent 포트 | 공식 |
| 162 |  | UDP | SNMP - Manager 포트 | 공식 |
| 179 | TCP |  | BGP (Border Gateway Protocol) | 공식 |
| 194 | TCP |  | IRC (Internet Relay Chat) | 공식 |
| 220 | TCP |  | 인터넷 메시지 접속 프로토콜 3 (IMAP3, Internet Message Access Protocol 3) |  |
| 389 | TCP |  | LDAP (Lightweight Directory Access Protocol) | 공식 |
| 443 | TCP |  | HTTPS - 보안 소켓 레이어 (SSL, Secure Socket Layer) 위의 HTTP (암호화 전송) | 공식 |
| 445 | TCP |  | Microsoft-DS (액티브 디렉터리, 윈도 공유, Sasser-worm, Agobot, Zobotworm) | 공식 |
| 445 |  | UDP | Microsoft-DS SMB 파일 공유 | 공식 |
| 465 | TCP |  | SSL 위의 SMTP - Cisco 프로토콜과 충돌 | 비공식, 충돌 |
| 514 |  | UDP | syslog 프로토콜 - 시스템 로그 작성 | 공식 |
| 515 | TCP |  | LPD 프로토콜 - 라인 프린터 데몬 서비스 | 공식 |
| 540 | TCP |  | UUCP (Unix-to-Unix Copy Protocol) | 공식 |
| 542 | TCP | UDP | 상용 (Commerce Applications) (RFC maintained by: Randy Epstein [repstein at host.net]) | 공식 |
| 587 | TCP |  | email message submission (SMTP) (RFC 2476) | 공식 |
| 591 | TCP |  | 파일메이커 6.0 Web Sharing (HTTP Alternate, see port 80) | 공식 |
| 631 | TCP |  | 인터넷 프린팅 프로토콜 | 공식 |
| 636 | TCP |  | SSL 위의 LDAP (암호화된 전송) | 공식 |
| 666 | TCP |  | id 소프트웨어의 둠 멀티플레이어 게임 | 공식 |
| 873 | TCP |  | rsync 파일 동기화 프로토콜 | 공식 |
| 981 | TCP |  | SofaWare Technologies Checkpoint Firewall-1 소프트웨어 내장 방화벽의 원격 HTTPS 관리 | 비공식 |
| 990 | TCP |  | SSL 위의 FTP (암호화 전송) | 공식 |
| 992 | TCP |  | SSL 위의 Telnet (암호화 전송) | 공식 |
| 993 | TCP |  | SSL 위의 IMAP4 (암호화 전송) | 공식 |
| 995 | TCP |  | SSL 위의 POP3 (암호화 전송) | 공식 |

## 3. 등록된 포트
포트 번호 1024번에서 49151번은 등록된 포트 구간이다. 이 포트들은 RFC6335에 따라 인터넷 할당 번호 관리기관에 등록한다. 통상 이 구간의 포트들은 시스템에서 슈퍼유저 권한이 없어도 사용 가능하다.

| 포트 | TCP | UDP | 설명 | 상태 |
|:---:|:---:|:---:|:---:|:---:|
| 1080 | TCP | UDP | SOCKS 프록시 | 공식 |
| 1194 | TCP | UDP | OpenVPN | 공식 |
| 1900 | TCP | UDP | Simple Service Discovery Protocol (SSDP), UPnP 장치 검출 서비스 | 공식 |
| 3306 | TCP |  | MySQL | 공식 |
| 3479 | TCP | UDP | 플레이스테이션 네트워크 | 공식 |
| 3480 | TCP | UDP | 플레이스테이션 네트워크 | 공식 |
| 3690 | TCP | UDP | Subversion | 공식 |
| 5228 | TCP | UDP | HP Virtual Room Service | 공식 |
| 5228 | TCP | UDP | 구글 안드로이드 메시징 프로토콜 | 비공식 |
| 5353 |  | UDP | Multicast DNS | 공식 |
| 6379 | TCP |  | Redis 서비스 | 공식 |
| 9100 | TCP |  | 네트워크 프린팅 프로토콜 | 비공식 |
| 17500 | TCP | UDP | Dropbox LanSync 프로토콜, LAN 상에서 Dropbox 클라이언트끼리 파일목록을 동기화 함 | 공식 |

## [출처 및 참고]
* <https://ko.wikipedia.org/wiki/TCP/UDP%EC%9D%98_%ED%8F%AC%ED%8A%B8_%EB%AA%A9%EB%A1%9D>
