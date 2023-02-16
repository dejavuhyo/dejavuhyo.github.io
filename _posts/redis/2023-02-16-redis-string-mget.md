---
title: Redis String Mget 명령
author: dejavuhyo
date: 2023-02-16 20:45:00 +0900
categories: [Database, Redis]
tags: [redis-string-mget, redis-command, 레디스, 레디스-mget, 레디스-명령]
---

## 1. Mget 명령
`MGET` 명령은 지정된 모든 키의 값을 가져오는 데 사용된다. 문자열 값을 보유하지 않거나 존재하지 않는 모든 키에 대해 특수 값 nil이 반환된다.

## 2. 반환 값
배열, 지정된 키의 값 목록이 반환 된다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> MGET KEY1 KEY2 .. KEYN
```

### 1) Example

```shell
redis 127.0.0.1:6379> SET key1 "hello"
OK
redis 127.0.0.1:6379> SET key2 "world"
OK
redis 127.0.0.1:6379> MGET key1 key2 someOtherKey
1) "Hello"
2) "World"
3) (nil)
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/strings_mget.htm](https://www.tutorialspoint.com/redis/strings_mget.htm)
