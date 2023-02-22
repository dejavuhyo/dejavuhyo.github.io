---
title: Linux에 Redis 설치
author: dejavuhyo
date: 2022-04-08 13:00:00 +0900
categories: [Database, Redis]
tags: [install-redis-on-linux, install-redis, linux-redis-설치, redis-설치, 리눅스-레디스-설치, 레디스-설치]
---

## 1. Linux에 Redis 설치
Ubuntu, RHEL 및 CentOS에 Redis를 설치하는 방법이다.

## 2. Ubuntu에 설치
packages.redis.io 공식 APT 저장소에서 최신 안정 버전의 Redis를 설치할 수 있다. 인덱스에 리포지토리를 추가하고 apt업데이트한 후 다음을 설치한다.

```shell
$ curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

$ echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

$ sudo apt-get update
$ sudo apt-get install redis
```

## 3. Snapcraft에 설치
[Snapcraft 스토어](https://snapcraft.io/store)는 수십 개의 Linux 배포판을 위한 [Redis 설치 패키지](https://snapcraft.io/redis)를 제공한다. Snapcraft를 사용하여 CentOS에 Redis를 설치하는 방법은 다음과 같다.

```shell
$ sudo yum install epel-release
$ sudo yum install snapd
$ sudo systemctl enable --now snapd.socket
$ sudo ln -s /var/lib/snapd/snap /snap
$ sudo snap install redis
```

## [출처 및 참고]
* [https://redis.io/docs/getting-started/installation/install-redis-on-linux/](https://redis.io/docs/getting-started/installation/install-redis-on-linux/)
