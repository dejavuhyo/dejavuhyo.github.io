---
title: Redis Keys Move 명령
author: dejavuhyo
date: 2023-02-03 19:10:00 +0900
categories: [Database, Redis]
tags: [redis-keys-move, redis, redis-command, 레디스, 레디스-키-이동, 레디스-명령]
---

## 1. Move 명령
`MOVE` 명령은 현재 선택된 데이터베이스에서 지정된 대상 데이터베이스로 키를 이동하는 데 사용된다.

## 2. 반환 값
정숫값 `1`은 키가 이동된 경우이고, `0`은 키가 이동되지 않은 경우이다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> MOVE KEY_NAME DESTINATION_DATABASE 
```

### 1) Example
먼저 Redis에서 키를 만들고 여기에 값을 설정한다.

```shell
redis 127.0.0.1:6379> SET tutorial1 redis
OK
```

Redis에서는 기본적으로 0번째 데이터베이스가 선택되어 있으므로 이제 생성된 키를 두 번째 데이터베이스로 이동한다.

```shell
redis 127.0.0.1:6379> MOVE tutorial1 1
1) (integer) 1
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/keys_move.htm](https://www.tutorialspoint.com/redis/keys_move.htm)
