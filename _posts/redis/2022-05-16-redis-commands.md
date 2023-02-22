---
title: Redis 명령어
author: dejavuhyo
date: 2022-05-16 14:15:00 +0900
categories: [Database, Redis]
tags: [redis-commands, redis-명령어, 레디스-명령어, redis, 레디스]
---

## 1. String
가장 기본적인 Value 타입이다.

### 1) 저장

* **`set {key} {value}`**: key, value를 저장

* **`mset {key} {value} [{key} {value} ...]`**: 여러 개의 key, value를 한 번에 저장

* **`setex {key} {seconds} {value}`**: key, seconds, value 저장 (설정한 시간 뒤에 소멸)

### 2) 조회

* **`keys *`**: 현재 저장된 키값들을 모두 확인 (부하가 심한 명령어라 운영 중인 서비스에선 절대 사용하면 안 됨)

* **`get {key}`**: 지정한 key에 해당하는 value를 가져옴

* **`mget {key} [{key} ...]`**: 여러 개의 key에 해당하는 value를 한 번에 가져옴

* **`ttl {key}`**: key의 만료 시간을 초 단위로 보여줌 (-1은 만료 시간 없음, -2 는 데이터 없음)

* **`pttl {key}`**: key의 만료 시간을 밀리초 단위로 보여줌

* **`type {key}`**: 해당 key의 value 타입 확인

### 3) 삭제

* **`del {key} [{key} ...]`**: 해당 key 들을 삭제

### 4)수정

* **`rename {key} {newKey}`**: key 이름 변경

* **`expire {key} {seconds}`**: 해당 key 값의 만료 시간 설정

### 5) 기타

* **`randomkey`**: 랜덤한 key 반환

* **`ping`**: 연결 여부 확인 ("ping"만 입력하면 "PONG"이라는 응답이 옴)

* **`dbsize`**: 현재 사용 중인 DB의 key의 개수 리턴

* **`flushall`**: 레디스 서버의 모든 데이터 삭제

* **`flushdb`**: 현재 사용 중인 DB의 모든 데이터 삭제

## 2. Set
Redis에서는 Set에 포함된 값들을 멤버라고 표현한다. 여러 멤버가 모여 집합(Set)을 구성한다.

* **`sadd {key} {member} [{member} ...]`**: key 에 새로운 멤버들을 추가. key가 없으면 새로 만든다.

* **`smembers {key}`**: key에 설정된 모든 멤버 반환

* **`srem {key} {member [{member} ...]}`**: key에 포함된 멤버들 삭제. 없는 멤버 입력하면 무시됨

* **`scard {key}`**: key에 저장된 멤버 수를 반환

* **`sismember {key} {member}`**: member 가 해당 key에 포함되는지 검사

## 3. Hash
Redis에서 저장할 수 있는 자료구조 중에 Hash도 있다. Hash 자체를 나타내는 key와 해당 key에 포함된 field까지 사용해서 값을 조회 및 저장할 수 있다.

* **`hset {key} {field} {value} [{field} {value} ...]`**: key 를 이름으로 한 Hash 자료 구조에 field 와 value 값을 저장

* **`hget {key} {field}`**: key Hash 값에 포함된 field의 value를 가져옴

* **`hdel {key} {field} [{field} ...]`**: field 값으로 데이터 삭제

* **`hlen {key}`**: Hash가 갖고 있는 field 개수 반환

* **`hkeys {key}`**: Hash가 갖고 있는 모든 field 출력

* **`hvals {key}`**: Hash가 갖고 있는 모든 value 출력

* **`hgetall {key}`**: Hash가 갖고 있는 모든 field와 value 출력

> 모든 명령어는 [Redis Commands](https://redis.io/commands/)를 참고한다.

[출처 및 참고]
* [https://redis.io/commands/](https://redis.io/commands/)
* [https://bcp0109.tistory.com/327](https://bcp0109.tistory.com/327)
