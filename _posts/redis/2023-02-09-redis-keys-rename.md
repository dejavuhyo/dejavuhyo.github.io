---
title: Redis Keys Rename 명령
author: dejavuhyo
date: 2023-02-09 21:30:00 +0900
categories: [Database, Redis]
tags: [redis-keys-rename, redis, redis-command, 레디스, 레디스-키-이름-바꾸기, 레디스-명령]
---

## 1. Rename 명령
`RENAME` 명령은 키 이름을 변경하는 데 사용된다.

## 2. 반환 값
문자열 응답 확인 또는 오류로, 이전 키와 새 키 이름이 같거나 키가 존재하지 않으면 오류를 반환한다. 새로운 키가 이미 있으면 기존 키를 덮어쓴다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> RENAME OLD_KEY_NAME NEW_KEY_NAME 
```

### 1) Example
먼저 Redis에서 일부 키를 생성하고 그 안에 일부 값을 설정한다.

```shell
redis 127.0.0.1:6379> SET tutorial1 redis
OK
```

이제 키 'tutorial1'의 이름을 'new-tutorial'로 바꾼다.

```shell
redis 127.0.0.1:6379> RENAME tutorial1 new-tutorial
OK
```

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/keys_rename.htm](https://www.tutorialspoint.com/redis/keys_rename.htm)
