---
title: 소스에서 Redis 설치
author: dejavuhyo
date: 2022-04-13 13:00:00 +0900
categories: [Database, Redis]
tags: [install-redis-from-source, install-redis, 소스-redis-설치, redis-설치, 소스-레디스-설치, 레디스-설치]
---

## 1. 소스에서 Redis 컴파일 및 설치
Linux 및 macOS를 비롯한 다양한 플랫폼 및 운영 체제에서 소스에서 Redis를 컴파일하고 설치할 수 있다. 레디스는 C 컴파일러와 libc 외에는 종속성이 없다.

## 2. 소스 파일 다운로드
Redis 소스 파일은에서 [다운로드 사이트](https://redis.io/download/)에서 받을 수 있다. [redis-hashes git 저장소](https://github.com/redis/redis-hashes)의 다이제스트와 비교하여 다운로드의 무결성을 확인할 수 있다.

Redis 다운로드 사이트에서 안정적인 최신 버전의 Redis에 대한 소스 파일을 얻으려면 다음을 실행한다.

```shell
$ wget https://download.redis.io/redis-stable.tar.gz
```

## 3. 레디스 컴파일
Redis를 컴파일하려면 먼저 tarball을 루트 디렉터리로 변경한 다음 make를 실행한다.

```shell
$ tar -xzvf redis-stable.tar.gz
$ cd redis-stable
$ make
```

컴파일이 성공하면 src 디렉터리에서 다음을 포함한 여러 Redis 바이너리를 찾을 수 있다.

* **redis-server:** Redis 서버 자체

* **redis-cli:** Redis와 통신하기 위한 명령줄 인터페이스 유틸리티

이 바이너리를 `/usr/local/bin`에 설치하려면 다음을 실행한다.

```shell
$ make install
```

## 4. 포그라운드에서 Redis 시작 및 중지
설치가 완료되면 다음을 실행하여 Redis를 시작할 수 있다.

```shell
$ redis-server
```

성공하면 Redis의 시작 로그가 표시되고 Redis가 포그라운드에서 실행된다.

Redis를 중지하려면 `Ctrl+C`를 입력한다.

## [출처 및 참고]
* [https://redis.io/docs/getting-started/installation/install-redis-from-source/](https://redis.io/docs/getting-started/installation/install-redis-from-source/)
