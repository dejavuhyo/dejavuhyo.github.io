---
title: Redis String Getset 명령
author: dejavuhyo
date: 2023-02-15 19:55:00 +0900
categories: [Database, Redis]
tags: [redis-string-getset, redis, redis-command, 레디스, 레디스-getset, 레디스-명령]
---

## 1. Getset 명령
`GETSET` 명령은 Redis 키에 지정된 문자열 값을 설정하고 이전 값을 반환한다.

## 2. 반환 값
간단한 문자열로, 키의 이전 값, 키가 존재하지 않으면 `nil`이 반환된다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> GETSET KEY_NAME VALUE
```

### 1) Example

```shell
redis 127.0.0.1:6379> GETSET mynewkey "This is my test key"
(nil)
redis 127.0.0.1:6379> GETSET mynewkey "This is my new value to test getset"
"This is my test key"
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/strings_getset.htm](https://www.tutorialspoint.com/redis/strings_getset.htm)
