---
title: Redis Keys Persist 명령
author: dejavuhyo
date: 2023-02-08 19:35:00 +0900
categories: [Database, Redis]
tags: [redis-keys-persist, redis, redis-command, 레디스, 레디스-키-지속, 레디스-명령]
---

## 1. Persist 명령
`PERSIST` 명령은 키에서 만료를 제거하는 데 사용된다.

## 2. 반환 값
정숫값 `1`은 키에서 제한 시간이 제거된 경우이고, `0`은 키가 존재하지 않거나 연관된 제한 시간이 없는 경우이다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> PERSIST KEY_NAME
```

### 1) Example
먼저 Redis에서 키를 만들고 여기에 값을 설정한다.

```shell
redis 127.0.0.1:6379> SET tutorial1 redis
OK
```

이제 키 만료를 설정하고 나중에 만료를 제거한다.

```shell
redis 127.0.0.1:6379> EXPIRE tutorial1 60 
1) (integer) 1 
redis 127.0.0.1:6379> TTL tutorial1 
1) (integer) 60 
redis 127.0.0.1:6379> PERSIST tutorial1 
1) (integer) 1 
redis 127.0.0.1:6379> TTL tutorial1 
1) (integer) -1
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/keys_persist.htm](https://www.tutorialspoint.com/redis/keys_persist.htm)
