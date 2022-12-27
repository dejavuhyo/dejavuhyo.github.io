---
title: Windows에 Redis 설치
author: dejavuhyo
date: 2022-04-12 11:20:00 +0900
categories: [Database, Redis]
tags: [install-redis-on-windows, install-redis, windows-redis-설치, redis-설치, 윈도우-레디스-설치, 레디스-설치]
---

## 1. Windows에 Redis 설치
Redis는 Windows에서 공식적으로 지원되지 않는다. 그러나 아래 지침에 따라 개발용 Windows에 Redis를 설치할 수 있다.

## 2. 설치 파일 이용
Windows에서 사용하려면, 기존 Redis를 Windows 환경에 맞춰 사용 할 수 있도록 바꿔야 하는데, github에 [Redis for Windows](https://github.com/microsoftarchive/redis/releases)를 릴리즈 해주는 팀이 있다.

설치 폴더 내의 redis-server.exe를 실행하면 redis 서버가 구동되며, 기본적으로 Listen Port는 6379 Port이다.

## 3. WSL2 이용
Windows에 Redis를 설치하려면 먼저 WSL2 (Linux용 Windows 하위 시스템)를 활성화해야 한다. WSL2를 사용하면 Windows에서 기본적으로 Linux 바이너리를 실행할 수 있다. 이 방법이 작동하려면 Windows 10 버전 2004 이상 또는 Windows 11을 실행해야 한다.

Microsoft는 [WSL 설치에 대한 자세한 지침](https://docs.microsoft.com/en-us/windows/wsl/install)을 제공한다. 이 지침을 따르고 설치되는 기본 Linux 배포에 주의한다. 이 가이드는 Ubuntu를 가정하고 있다.

### 1) 레디스 설치
Windows에서 Ubuntu를 실행하고 나면 `apt-get`을 사용하여 Redis를 설치할 수 있다.

```shell
$ sudo apt-add-repository ppa:redislabs/redis
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install redis-server
```

다음과 같이 Redis 서버를 시작한다.

```shell
$ sudo service redis-server start
```

### 2) 레디스에 연결
Redis CLI에 연결하여 Redis 서버가 실행 중인지 테스트할 수 있다.

```shell
$ redis-cli
127.0.0.1:6379> ping
PONG
```

## [출처 및 참고]
* <https://redis.io/docs/getting-started/installation/install-redis-on-windows/>
* <https://sunghs.tistory.com/90>
