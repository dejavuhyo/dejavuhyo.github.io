---
title: macOS에 Redis 설치
author: dejavuhyo
date: 2022-04-11 18:30:00 +0900
categories: [Database, Redis]
tags: [install-redis-on-mac-os, install-redis, linux-redis-설치, redis-설치, macOS-레디스-설치, 레디스-설치]
---

## 1. macOS에 Redis 설치
Homebrew를 사용하여 macOS에서 Redis 설치 및 시작한다.

Homebrew를 사용하여 macOS에 Redis를 설치하는 방법을 보여준다. Homebrew는 macOS에 Redis를 설치하는 가장 쉬운 방법이다. macOS의 소스 파일에서 Redis를 빌드하려면 [소스에서 Redis 설치](https://redis.io/docs/getting-started/installation/install-redis-from-source/)를 참조한다.

## 2. 전제 조건
Homebrew가 설치되어 있는지 확인한다. 터미널에서 다음을 실행한다.

```shell
$ brew --version
```

이 명령이 실패하면 [Homebrew 설치 지침](https://brew.sh/)을 따라 Homebrew를 설치한다.

## 3. 설치
터미널에서 다음을 실행한다.

```shell
$ brew install redis
```

시스템에 Redis가 설치된다.

### 4. 포그라운드에서 Redis 시작 및 중지
Redis 설치를 테스트하려면 redis-server 명령줄에서 실행 파일을 실행할 수 있다.

### 1) 실행

```shell
$ redis-server
```

성공하면 Redis의 시작 로그가 표시되고 Redis가 포그라운드에서 실행된다.

포그라운드에서 Redis를 실행하는 대신 launchd를 사용하여 프로세스를 백그라운드에서 시작할 수도 있다.

```shell
$ brew services start redis
```

이렇게 하면 Redis가 시작되고 로그인 시 다시 시작된다.

### 2) 상태 확인
launchd 실행하여 관리되는 Redis의 상태를 확인할 수 있다.

```shell
$ brew services info redis
```

서비스가 실행 중이면 다음과 같은 출력이 표시된다.

```text
redis (homebrew.mxcl.redis)
Running: ✔
Loaded: ✔
User: miranda
PID: 67975
```

### 3) 중지
Redis를 중지하려면 `Ctrl+C`를 입력한다. launchd를 사용하여 중지할 수 있다.

서비스를 중지하려면 다음을 실행한다.

```shell
$ brew services stop redis
```

## 5. 레디스에 연결
Redis가 실행되면 `redis-cli`를 실행하여 테스트할 수 있다.

```shell
$ redis-cli
```

Redis REPL이 열린다. 몇 가지 명령을 실행해 보면 아래와 같다.

```shell
127.0.0.1:6379> lpush demos redis-macOS-demo
OK
127.0.0.1:6379> rpop demos
"redis-macOS-demo"
```

## [출처 및 참고]
* <https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/>
