---
title: Linux 셸
author: dejavuhyo
date: 2021-10-19 06:00:00 +0900
categories: [DevOps, Linux]
tags: [secure-shell, ssh, 시큐어-셜]
---

## 1. 시큐어 셸이란
시큐어 셸(Secure SHell, SSH)은 네트워크상의 다른 컴퓨터에 로그인하거나 원격 시스템에서 명령을 실행하고 다른 시스템으로 파일을 복사할 수 있도록 해 주는 응용 프로그램 또는 그 프로토콜을 가리킨다. 기존의 rsh, rlogin, 텔넷 등을 대체하기 위해 설계되었으며, 강력한 인증 방법 및 안전하지 못한 네트워크에서 안전하게 통신을 할 수 있는 기능을 제공한다. 기본적으로는 22번 포트를 사용한다.

SSH는 암호화 기법을 사용하기 때문에, 통신이 노출된다고 하더라도 이해할 수 없는 암호화된 문자로 보인다.

SSH는 버클리 서비스들(rsh, rcp, rlogin, rexec)및 telnet, ftp 서비스를 좀 더 보안이 강화된 명령으로 대체하기 위해 만들었다. 1995년 핀란드의 Tatu Ylönen이 개발해 공개했고, 많은 반향을 일으켰다. 1995년 말 Ylönen은 SSH Communications Security 사를 설립하고, 상용화되었다. 소스 코드는 공개되었지만 여러 가지 제약이 있었다고 하는데, 이에 SSH 1.2.12 릴리즈를 기반으로 OpenSSH 프로젝트가 결성되고 BSD 라이선스로 개발하기 시작했다. OpenSSH가 처음 선보인 건 1999년 OpenBSD 2.6 릴리즈부터이다. 이후 계속 보완되어 오늘날에 이르게 되었다.

## 2. 동작 방식
SSH 키(Key)는 공개키(public key)와 비공개키(private key)로 이루어지는데 이 두 개의 관계를 이해하는 것이 SSH Key를 이해하는데 핵심이다. 키를 생성하면 공개키와 비공개키가 만들어진다. 이 중에 비공개키는 로컬 머신에 위치해야 하고, 공개키는 리모트 머신에 위치해야 한다. (로컬 머신은 SSH Client, 원격 머신은 SSH Server가 설치된 컴퓨터를 의미한다) SSH 접속을 시도하면 SSH Client가 로컬 머신의 비공개 키와 원격 머신의 비공개키를 비교해서 둘이 일치하는지를 확인한다.

![ssh](/assets/img/2021-10-19-secure-shell/ssh.png)

## 3. SSH 통신방법
서버에 접속할 때 비밀번호 대신 Key를 제출하는 방식으로 비밀번호보다 높은 수준 보안방식이다.

SSH key는 공개키(public key), 비공개키(private key)로 이루어지며, 키를 생성하면 공개키와 비공개 키가 만들어진다. 비공개키는 로컬에 위치하고, 공개키는 원격지에 위치한다. SSH 접속을 시도하면 로컬의 비공개 키와 원격 서버의 비공개키를 비교해서 일치하는지 인증과정을 거치고 접속을 할 수 있게 해준다.

> **public key:** 공개되어도 안전한 키, 복호화 불가

> **private key:** 외부 노출되면 안되는 키, 로컬 컴퓨터 내부에 저장, 암호화 정보 복호화

## [출처 및 참고]
* [https://ko.wikipedia.org/wiki/%EC%8B%9C%ED%81%90%EC%96%B4_%EC%85%B8](https://ko.wikipedia.org/wiki/%EC%8B%9C%ED%81%90%EC%96%B4_%EC%85%B8)
* [https://velog.io/@hyeseong-dev/%EB%A6%AC%EB%88%85%EC%8A%A4-ssh%EB%9E%80](https://velog.io/@hyeseong-dev/%EB%A6%AC%EB%88%85%EC%8A%A4-ssh%EB%9E%80)
* [https://developsd.tistory.com/114](https://developsd.tistory.com/114)
