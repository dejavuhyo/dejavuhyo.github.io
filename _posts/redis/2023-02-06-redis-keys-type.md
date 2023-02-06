---
title: Redis Keys Type 명령
author: dejavuhyo
date: 2023-02-06 20:15:00 +0900
categories: [Database, Redis]
tags: [redis-keys-type, redis, redis-command, 레디스, 레디스-키-유형, 레디스-명령]
---

## 1. Type 명령
`TYPE` 명령은 키에 저장된 값의 데이터 유형을 가져오는 데 사용된다.

## 2. 반환 값
문자열 응답으로, 키에 저장된 값의 데이터 유형 또는 없음이다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> TYPE KEY_NAME 
```

### 1) Example
먼저 Redis에서 일부 키를 생성하고 그 안에 일부 값을 설정한다.

```shell
redis 127.0.0.1:6379> SET tutorial1 redis
OK
```

이제 키 유형을 확인한다.

```shell
redis 127.0.0.1:6379> TYPE tutorial1
string
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/keys_type.htm](https://www.tutorialspoint.com/redis/keys_type.htm)
