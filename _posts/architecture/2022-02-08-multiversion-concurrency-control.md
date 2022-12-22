---
title: 다중 버전 동시성 제어
author: dejavuhyo
date: 2022-02-08 10:00:00 +0900
categories: [Application, Architecture]
tags: [multiversion-concurrency-control, mcc, mvcc, 다중-버전-동시성-제어]
---

## 1. 다중 버전 동시성 제어란
다중 버전 동시성 제어(multiversion concurrency control, MCC, MVCC), 다중 버전 병행 수행 제어는 데이터베이스 관리 시스템이 일반적으로 사용하는 동시성 제어 방식으로, 데이터베이스로의 동시 접근을 제공하고 프로그래밍 언어에서 트랜잭셔널 메모리를 구현한다.

동시성 제어 없이 누군가가 데이터베이스를 읽는 동시에 다른 누군가가 기록을 하려고 한다면 독자는 반쯤 기록되거나 일관성이 없는 데이터를 보게 될 가능성이 있다.

MVCC 데이터베이스가 데이터의 업데이트가 필요할 때, 기존 데이터 항목을 새로운 데이터가 덮어쓰는 대신 데이터 항목의 새로운 버전을 만든다. 즉, 여러 버전이 저장된다.

각 트랜잭션이 주시하는 버전은 구현된 격리 레벨에 따른다. MVCC로 구현된 가장 흔한 격리 레벨은 스냅샷 격리이다. 스냅샷 격리를 사용하는 경우 트랜잭션은 트랜잭션이 시작할 당시처럼 데이터의 상태를 관찰한다.

MVCC는 포인트-인-타임 일관성(point-in-time consistent) 뷰를 제공한다. MVCC 상태에서 읽기 트랜잭션은 일반적으로 타임스탬프나 트랜잭션 ID를 사용하여 읽을 DB의 상태를 결정하고 데이터의 버전들을 읽는다. 그러므로 읽기, 쓰기 트랜잭션은 락(lock)의 필요 없이 다른 트랜잭션과 격리된다.

그러나 락이 불필요함에도 불구하고 오라클 등 일부 MVCC 데이터베이스에서는 락이 사용된다. 쓰기는 더 새로운 버전을 만드는 반면, 동시성 읽기는 더 오래된 버전에 접근한다.

## 2. 데이터의 다중버전 상태
아래의 단계를 예를들어 설명한다.

* **Step 1:** a 에는 30이라는 데이터가 저장되어 있다.

* **Step 2:** 트랜잭션1에서 a는 50으로 데이터를 갱신했다. 이 값을 commit 하기 전이다.

* **Step 3:** 트랜잭션2에서 a는 30으로 데이터가 조회된다.

| 단계 | 트랜잭션1 | 트랜잭션2 |
|:-----:|:-----:|:-----:|
| Step 1 | a = 30 |  |
| Step 2 | begin tran |  |
| Step 3 | update a = 50 |  |
| Step 4 |  | select a = 30 |
| Step 5 | select a = 50 |  |
| Step 6 | commit |  |
| Step 7 |  | select a = 50 |

트랜잭션1에서 데이터를 갱신했지만, 트랜잭션2는 여전히 이전의 값을 조회하고 있다.

데이터를 다중버전 상태로 유지하고 있어 Commit 전까지는 이전의 값을 Undo 영역에 유지하고 있기 때문에 값을 조회할 수 있다.

## 3. MVCC의 2가지 접근법

### 1) 첫 번째 접근법
PostgreSQL, Interbase, SQL Server의 DB에 해당하는 방식이다. 데이터베이스 내에 다중 버전의 데이터를 저장하고, 더이상 필요하지 않을 때 데이터를 정리한다.

데이터베이스 내에 다중 버전의 데이터가 저장되기 때문에, 데이터가 많아지는 형식으로 되어 파일 사이즈가 계속 증가하게 된다. 그리고 기존의 데이터는 삭제 표시가 생기게 된다.

![first-approach](/assets/img/2022-02-08-multiversion-concurrency-control/first-approach.png)

### 2) 두 번째 접근법
Oracle, MySQL DB에 해당하는 방식이다. 최신 버전의 데이터만 데이터베이스 내에 저장하고, 이전의 데이터는 Undo를 이용하여 데이터를 저장한다.

데이터가 갱신되면 Undo 영역에는 이전 데이터 블럭들 값과 당시의 SCN(System Commit Number)이 저장되어 있다.

조회 시 새로운 최신 SCN의 값을 가지며, 이 SCN 값 이전의 블록들 값을 읽게 된다.

![second-approach](/assets/img/2022-02-08-multiversion-concurrency-control/second-approach.png)

## 4. MVCC 관리 방법

|  | PostgreSQL | Oracle | SQL Server |
|:-----:|:-----:|:-----:|:-----:|
| 오래된 버전 저장공간 | Heap/Index 영역 | Undo 영역 | tempdb 영역 |
| 정리 방법 | Vacuum | System Monitor Process | Ghost Cleanup Task |

## 5. MVCC 저장 방법

### 1) 페이지
데이터 저장공간인 Page에는 페이지 헤더, 튜플, 튜플의 헤더, 튜플의 위치를 가리키는 포인터들로 저장된다.

각각의 튜플에는 데이터들이 저장되어 있다. 그리고 이 튜플의 헤더는 튜플의 속성값들을 저장한다.

![page](/assets/img/2022-02-08-multiversion-concurrency-control/page.png)

### 2) 튜플의 헤더
xmin은 최신의 값, tmax는 이전의 값을 의미한다.

![header](/assets/img/2022-02-08-multiversion-concurrency-control/header.png)

### 3) 저장 방법

#### (1) Insert
세션0에서 Tuple1에 A라고 하는 데이터를 저장 후 Commit 한다. t_xmin의 값은 2a9이고, 아직 갱신이 되지 않은 값이기 때문에 t_xmax는 0이다. 이 상태에서 세션1에서 조회를 하면 t_xmin 2a9에 있는 값을 조회한다.

![insert](/assets/img/2022-02-08-multiversion-concurrency-control/insert.png)

#### (2) Update
위 상태에서 세션 0에서 Tuple1의 A 값을 B로 Update 한다. Tuple1, B에 새로운 값이 저장되었고 t_xmin은 2aa 값이 되었다.

Tuple1, A에는 t_xmax에서 갱신되었다는 의미로 다음 값인 2aa를 저장한다. 이제 새로 조회하는 세션들은 갱신된 2aa의 값을 조회하게 된다.

![update](/assets/img/2022-02-08-multiversion-concurrency-control/update.png)

## 6. MVCC 조회 방법

### 1) Oracle
시간정보인 'SCN'과 공간정보인 'XID'를 복합적으로 사용하여 트랜잭션을 처리한다. Commit SCN은 트랜잭션이 완료될 때 부여된다.

XID는 xmin, xmax 값들을 구분하기 위한 고윳값이다

### 2) PostgreSQL, MySQL
트랜잭션이 시작될 때 XID가 붙여지기 때문에 각각의 snapshot 시점이 필요하다.

![snapshot-structure](/assets/img/2022-02-08-multiversion-concurrency-control/snapshot-structure.png)

## [출처 및 참고]
* [https://ko.wikipedia.org/wiki/%EB%8B%A4%EC%A4%91_%EB%B2%84%EC%A0%84_%EB%8F%99%EC%8B%9C%EC%84%B1_%EC%A0%9C%EC%96%B4](https://ko.wikipedia.org/wiki/%EB%8B%A4%EC%A4%91_%EB%B2%84%EC%A0%84_%EB%8F%99%EC%8B%9C%EC%84%B1_%EC%A0%9C%EC%96%B4)
* [https://mozi.tistory.com/561](https://mozi.tistory.com/561)
* [https://www.slideshare.net/pgday_seoul/mvcc-in-postgre](https://www.slideshare.net/pgday_seoul/mvcc-in-postgre)
* [http://www.datanet.co.kr/news/articleView.html?idxno=116534](http://www.datanet.co.kr/news/articleView.html?idxno=116534)
