---
title: Ubuntu sudo, su 명령어
author: dejavuhyo
date: 2024-08-16 09:09:00 +0900
categories: [DevOps, Linux]
tags: [ubuntu-sudo,ubuntu-su, 우분투-계정, 우분투-계정-전환]
---

## 1. sudo 명령어
`sudo`는 SuperUser DO의 약자이다. 명령어 맨 앞에 붙이면 루트에 상응하는 권한으로 명령어를 수행할 수 있다.

예를 들어, 시스템 종료, 재부팅, 시스템 파일 편집, 방화벽 오픈, 패키지 설치, 일부 서비스 실행 등 작업에 필요하다.

`sudo` 사용시 현재 사용자의 패스워드를 입력한다. root 패스워드가 필요하지 않다.

root 계정의 비밀번호를 몰라도 루트에 상응하는 작업을 할 수 있다는 의미이다.

* root 계정처럼 사용하는 세션 전환 명령어

```shell
sudo -i
```

sudo는 리눅스를 좀 더 안전하게 사용할 수 있게 해준다.

## 2. su 명령어
`su`는 Switch User의 약자이다. 계정을 전환하는 명령어이다.

계정 전환시 전환하려는 계정의 비밀번호가 필요하다.

```shell
$ su - root
```

root 계정에서 일반 계정으로 전환시 비밀번호를 묻지 않는다.

일반 계정에서 root 계정 또는 일반 계정에서 일반 계정으로 전환시 전환하려는 계정의 비밀번호가 필요하다.

`-`를 사용하지 않으면 현재 계정의 환경 변수를 그대로 사용한다.

```shell
$ su root
```

## [출처 및 참고]
* [https://change-words.tistory.com/entry/리눅스-su-sudo-차이](https://change-words.tistory.com/entry/리눅스-su-sudo-차이)
