---
title: Redis Keys Del 명령
author: dejavuhyo
date: 2023-01-30 20:35:00 +0900
categories: [Database, Redis]
tags: [redis-keys-del, redis, redis-command, 레디스, 레디스-키-삭제, 레디스-명령]
---

## 1. Del 명령
`DEL` 명령은 Redis에서 기존 키를 삭제하는 데 사용된다.

## 2. 반환 값
제거된 키의 수 이다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> DEL KEY_NAME 
```

### 1) Example
먼저 Redis에서 키를 만들고 여기에 값을 설정한다.

```shell
redis 127.0.0.1:6379> SET tutorialspoint redis
OK
```

이제 이전에 만든 키를 삭제한다.

```shell
redis 127.0.0.1:6379> DEL tutorialspoint
(integer) 1
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/keys_del.htm](https://www.tutorialspoint.com/redis/keys_del.htm)
