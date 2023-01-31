---
title: Redis Keys Dump 명령
author: dejavuhyo
date: 2023-01-31 20:00:00 +0900
categories: [Database, Redis]
tags: [redis-keys-dump, redis, redis-command, 레디스, 레디스-키-덤프, 레디스-명령]
---

## 1. Dump 명령
`DUMP` 명령은 Redis의 지정된 키에 저장된 데이터의 직렬화된 버전을 가져오는 데 사용된다.

## 2. 반환 값
직렬화된 값(문자열)

## 3. Syntax

```shell
redis 127.0.0.1:6379> DUMP KEY_NAME
```

### 1) Example
먼저 Redis에서 키를 만들고 여기에 값을 설정한다.

```shell
redis 127.0.0.1:6379> SET tutorialspoint redis
OK
```

이제 이전에 생성한 키의 덤프를 생성한다.

```shell
redis 127.0.0.1:6379> DUMP tutorialspoint
"\x00\x05redis\x06\x00S\xbd\xc1q\x17z\x81\xb2"
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/keys_dump.htm](https://www.tutorialspoint.com/redis/keys_dump.htm)
