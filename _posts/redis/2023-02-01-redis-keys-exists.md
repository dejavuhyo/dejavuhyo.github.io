---
title: Redis Keys Exists 명령
author: dejavuhyo
date: 2023-02-01 19:40:00 +0900
categories: [Database, Redis]
tags: [redis-keys-exists, redis, redis-command, 레디스, 레디스-키-존재, 레디스-명령]
---

## 1. Exists 명령
`EXISTS` 명령은 Redis에 키가 존재하는지 여부를 확인하는 데 사용된다.

## 2. 반환 값
정수 값 `1`은 키가 존재하는 경우이고, `0`은 키가 존재하지 않는 경우이다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> EXISTS KEY_NAME
```

### 1) Example

```shell
redis 127.0.0.1:6379> EXISTS tutorialspoint-new-key
(integer) 0
```

이제 이름이 tutorialspoint-new-key인 키를 생성하고 존재 여부를 확인한다.

```shell
redis 127.0.0.1:6379> EXISTS tutorialspoint-new-key
(integer) 1
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/keys_exists.htm](https://www.tutorialspoint.com/redis/keys_exists.htm)
