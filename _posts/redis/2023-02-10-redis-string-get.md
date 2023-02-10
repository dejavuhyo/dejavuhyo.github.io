---
title: Redis String Get 명령
author: dejavuhyo
date: 2023-02-10 21:30:00 +0900
categories: [Database, Redis]
tags: [redis-string-set, redis, redis-command, 레디스, 레디스-문자열-설정, 레디스-명령]
---

## 1. Get 명령
`GET` 명령은 지정된 키에 저장된 값을 가져오는 데 사용된다. 키가 존재하지 않으면 `nil`이 반환됩니다. 반환된 값이 문자열이 아니면 오류가 반환된다.

## 2. 반환 값
간단한 문자열로, Value 혹은 key 혹은 nil이다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> GET KEY_NAME
```

### 1) Example
먼저 Redis에서 키를 설정한 다음 가져온다.

```shell
redis 127.0.0.1:6379> SET tutorialspoint redis 
OK 
redis 127.0.0.1:6379> GET tutorialspoint 
"redis"
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/strings_get.htm](https://www.tutorialspoint.com/redis/strings_get.htm)
