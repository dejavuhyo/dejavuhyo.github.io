---
title: Linux 도메인 정보 확인
author: dejavuhyo
date: 2021-08-27 06:00:00 +0900
categories: [DevOps, Linux]
tags: [nslookup, name-server, find-dns-records, dns-records, 도메인-정보-확인, 도메인-정보]
---

## 1. nslookup이란
nslookup은 네트워크 관리 명령줄 인터페이스 도구로서 많은 컴퓨터 운영 체제에서 사용 가능하며, 도메인 네임을 얻거나 IP 주소 매핑 또는 다른 특정한 DNS 레코드를 도메인 네임 시스템(DNS)에 질의할 때 사용된다.

BIND 9의 개발 이전에 인터넷 시스템 컨소시엄은 nslookup을 반대하고 host와 dig로 대체하려고 계획하였다. 그러나 이 결정은 2004년에 BIND 9.3의 배포와 함께 번복되었고 이후 nslookup은 완전하게 지원되고 있다.

![nslookup](/assets/img/2021-08-27-nslookup/nslookup.png)

nslookup의 이름은 'name server lookup'을 의미한다. nslookup은 운영 체제의 로컬 도메인 네임 시스템 리졸버 라이브러리를 사용해서 자신의 쿼리를 수행하지 않으므로 dig와는 다른 행동을 한다.

추가적으로 벤더에 의해 제공된 버전들은 다른 이름 정보의 소스의 결과를 사용하거나 포함함으로써 혼란을 일으킬 수 있다. (호스트 파일, 네트워크 정보 서비스 같은) 몇몇 행동들은 resolv.conf 파일의 내용에 따라 수정될 수 있다.

> 최신 리눅스는 [host 명령어](https://www.lesstif.com/system-admin/linux-host-89555912.html)를 사용하는 것을 권장하고 있다.

## 2. 기본 사용법

### 1) IP 조회

```shell
$ nslookup google.com

Server:         192.168.152.2
Address:        192.168.152.2#53
Non-authoritative answer:
Name:   google.com
Address: 58.229.92.103
Name:   google.com
Address: 58.229.92.88
```

### 2) MX(Mail Record) 확인

```shell
$ nslookup -query=mx google.com

Server:         192.168.152.2
Address:        192.168.152.2#53
Non-authoritative answer:
google.com      mail exchanger = 20 alt1.aspmx.l.google.com.
google.com      mail exchanger = 30 alt2.aspmx.l.google.com.
google.com      mail exchanger = 40 alt3.aspmx.l.google.com.
google.com      mail exchanger = 10 aspmx.l.google.com.
google.com      mail exchanger = 50 alt4.aspmx.l.google.com.
Authoritative answers can be found from:
```

### 3) CNAME 조회
Canonical Name 필드를 조회할 경우 -q=cname 옵션을 사용한다.

```shell
$ nslookup -q=cname example.com
```

### 4) TEXT 조회
Domain validation 이나 SPF(Sender Policy Framework) 설정시 사용하는 TEXT 필드를 조회한다.

```shell
$ nslookup -type=ns google.com 

Server:         192.168.152.2
Address:        192.168.152.2#53
Non-authoritative answer:
google.com      nameserver = ns1.google.com.
google.com      nameserver = ns3.google.com.
google.com      nameserver = ns2.google.com.
google.com      nameserver = ns4.google.com.
Authoritative answers can be found from:
```

### 5) Reverse DNS lookup
IP로 Domain Name을 찾는 기능이다.

옵션으로 `-type=ptr`를 준 것과 동일하며 찾는 주소가 IP 경우 nslookup이 자동으로 `-type=ptr`을 설정해서 검색한다. 찾으려는 서버가 속한 DNS의 설정에 PTR항목이 설정되어 있어야 조회가 가능하다.

```shell
$ nslookup 209.132.183.181

Server:         192.168.152.2
Address:        192.168.152.2#53
Non-authoritative answer:
181.183.132.209.in-addr.arpa    name = origin-www2.redhat.com.
Authoritative answers can be found from:
```

### 6) 특정 DNS 사용하여 조회
기본 설정된 DNS가 아닌 외부의 DNS를 지정해서 조회할 수 있다. 지정할 DNS는 조회할 도메인 이름 뒤에 써주면 된다. 다음은 구글의 공개 DNS인 8.8.8.8 을 사용하여 조회하는 예제이다.

```shell
$ nslookup redhat.com 8.8.8.8

Server:         8.8.8.8
Address:        8.8.8.8#53
Non-authoritative answer:
Name:   redhat.com
Address: 209.132.183.105
```

## [출처 및 참고]
* <https://ko.wikipedia.org/wiki/Nslookup>
* <https://www.lesstif.com/system-admin/nslookup-20775988.html>
* <https://www.thegeekstuff.com/2012/07/nslookup-examples/>
