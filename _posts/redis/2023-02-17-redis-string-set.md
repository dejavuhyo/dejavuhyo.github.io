---
title: Redis String Set 명령
author: dejavuhyo
date: 2023-02-17 22:35:00 +0900
categories: [Database, Redis]
tags: [redis-string-set, redis, redis-command, 레디스, 레디스-문자열-설정, 레디스-명령]
---

## 1. Set 명령
`SET` 명령은 Redis 키에 일부 문자열 값을 설정하는 데 사용된다.

## 2. 반환 값
간단한 문자열로, 값이 키에 설정되어 있으면 `OK`, 값이 설정되지 않은 경우 `Null`이다.

## 3. Syntax

```shell
redis 127.0.0.1:6379> SET KEY_NAME VALUE
```

### 1) Example

```shell
redis 127.0.0.1:6379> SET tutorialspoint redis
OK
```

## 4. Options
`SET` 명령에는 명령의 동작을 수정하는 많은 옵션이 있다. 다음은 사용할 수 있는 옵션이 있는 SET 명령의 기본 구문이다.

```shell
redis 127.0.0.1:6379> SET KEY VALUE [EX seconds] [PX milliseconds] [NX|XX]
```

* EX seconds: 지정된 만료 시간을 초 단위로 설정한다.

* PX milliseconds: 지정된 만료 시간을 밀리초 단위로 설정한다.

* NX: 아직 존재하지 않는 경우에만 키를 설정한다.

* XX: 이미 존재하는 경우에만 키를 설정한다.

### 1) 예시

```shell
redis 127.0.0.1:6379> SET tutorialspoint redis EX 60 NX
OK
```

위의 예에서는 키가 존재하지 않는 경우 만료 시간이 60초인 'tutorialspoint' 키를 설정한다.

[출처 및 참고]
* [https://www.tutorialspoint.com/redis/strings_set.htm](https://www.tutorialspoint.com/redis/strings_set.htm)
