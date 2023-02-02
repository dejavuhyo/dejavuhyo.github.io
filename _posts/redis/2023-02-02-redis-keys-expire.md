---
title: Redis Keys Expire 명령
author: dejavuhyo
date: 2023-02-02 20:30:00 +0900
categories: [Database, Redis]
tags: [redis-keys-expire, redis, redis-command, 레디스, 레디스-키-만료, 레디스-명령]
---

## 1. Expire 명령
`Expire` 명령은 키 만료를 설정하는 데 사용된다. 만료 시간이 지나면 Redis에서 키를 사용할 수 없다.

## 2. 반환 값
정숫값 `1`은 키에 시간 초과가 설정된 경우이고, `0`은 키가 존재하지 않거나 제한 시간을 설정할 수 없는 경우이다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> Expire KEY_NAME TIME_IN_SECONDS
```

### 1) Example
먼저 Redis에서 키를 만들고 여기에 값을 설정한다.

```shell
redis 127.0.0.1:6379> SET tutorialspoint redis
OK
```

이제 이전에 생성한 키의 타임아웃을 설정한다.

```shell
redis 127.0.0.1:6379> EXPIRE tutorialspoint 60
(integer) 1
```

위의 예에서는 핵심 tutorialspoint에 60초 시간이 설정되어 있다. 60초가 지나면 키가 자동으로 만료된다.

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/keys_expire.htm](https://www.tutorialspoint.com/redis/keys_expire.htm)
