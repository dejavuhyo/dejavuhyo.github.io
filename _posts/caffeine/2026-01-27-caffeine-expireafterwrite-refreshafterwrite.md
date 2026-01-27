---
title: Caffeine Cache의 expireAfterWrite와 refreshAfterWrite 동작 방식
author: dejavuhyo
date: 2026-01-27 10:50:00 +0900
categories: [DevOps, Caffeine]
tags: [caffeine, caffeine-cache, expireafterwrite, refreshafterwrite, 카페인, 카페인-캐시]
---

## 1. `expireAfterWrite` (만료: Expiration)
이 설정은 데이터가 캐시에 쓰여지거나(Created) 마지막으로 수정된(Updated) 후, 지정된 시간이 지나면 해당 데이터를 삭제(Eviction)하는 방식이다.

### 1) 동작 방식

① 캐시에 데이터 'A'가 저장된다 (Time = 0)

② `expireAfterWrite` 설정 시간이 지난다 (예: 10분)

③ 10분 후 'A'에 대한 요청이 들어온다.

④ Caffeine은 이 데이터가 만료되었다고 판단하고 즉시 삭제 한다.

⑤ 요청자는 데이터를 얻기 위해 '동기적(Synchronous)'으로 데이터를 다시 로드(DB 조회 등)해야 한다.

⑥ 로드되는 동안 요청 스레드는 대기(Blocking)한다.

### 2) 특징

* **물리적 삭제:** 시간이 지나면 캐시에서 데이터가 완전히 사라진다.

* **동기 로딩 (Blocking):** 만료 후 첫 요청은 데이터를 새로 가져올 때까지 기다려야 하므로 응답 지연(Latency)이 발생할 수 있다.

* **데이터 일관성:** 만료된 시점 이후에는 항상 최신 데이터를 보장하므로 일관성이 높다.

## 2. `refreshAfterWrite` (갱신: Refresh)
이 설정은 데이터가 캐시에 쓰여진 후 지정된 시간이 지나면, 해당 데이터를 '갱신이 필요한 상태'로 표시하지만, 삭제하지는 않는 방식이다.

### 1) 동작 방식

① 캐시에 데이터 'A'가 저장된다 (Time = 0)

② `refreshAfterWrite` 설정 시간이 지난다 (예: 10분)

③ 10분 후 'A'에 대한 요청이 들어온다.

④ Caffeine은 기존의(오래된) 데이터 'A'를 즉시 반환한다. (요청자는 기다리지 않음)

⑤ 동시에 백그라운드에서 '비동기적(Asynchronous)'으로 새로운 데이터를 로드하여 캐시를 갱신한다.

⑥ 갱신이 완료된 후 들어오는 요청부터는 새로운 데이터를 반환한다.

### 2) 특징

* **논리적 만료:** 데이터가 물리적으로 삭제되지 않고, 갱신 트리거가 발동될 뿐이다.

* **비동기 로딩 (Non-Blocking):** 갱신 작업이 백그라운드에서 일어나므로, 요청자는 느린 DB 조회를 기다릴 필요 없이 기존 데이터를 빠르게 받을 수 있다.

* **최종 일관성 (Eventual Consistency):** 갱신되는 짧은 순간 동안은 클라이언트가 '과거 데이터'를 볼 수 있다.

## 3. 차이점 비교
가장 큰 차이는 '만료 시점의 요청을 처리하는 방법(Blocking vs Non-Blocking)'이다.

| 특징 | expireAfterWrite (만료) | refreshAfterWrite (갱신) |
| --- | --- | --- |
| 기본 동작 | 시간이 지나면 '삭제'함 | 시간이 지나면 '비동기 갱신'을 시도함 |
| 요청 처리 | 만료 후 요청 시, 새 데이터를 로드할 때까지 대기(Blocking) | 갱신 필요 시, 기존 데이터 반환 후 백그라운드 로드 (Non-Blocking) |
| 반환 데이터 | 항상 최신 데이터 (없으면 로드함) | 갱신 중에는 구식(Stale) 데이터를 반환할 수 있음 |
| 필요 요소 | `CacheLoader` 없어도 됨 (수동 put 가능) | 반드시 `CacheLoader`가 설정되어야 함 (자동 로딩 필요) |
| 적합한 경우 | 데이터의 정확성이 속도보다 중요할 때 | 빠른 응답 속도가 중요하고, 약간의 과거 데이터 노출이 허용될 때 |

## 4. 두 가지를 함께 사용
**두 설정을 혼합**하여 사용하는 패턴이 매우 강력하다. 이를 통해 '빠른 응답'과 '데이터 신선도 유지'라는 장점이 있다.

### 1) 추천 설정

* 예시
  - `refreshAfterWrite = 1분`
  - `expireAfterWrite = 5분`

### 2) 이유

* **평상시 (Hot Data):** 데이터가 자주 조회된다면 1분마다 비동기로 갱신(Refresh)되어 사용자는 항상 빠른 응답을 받는다.

* **데이터가 오랫동안 조회되지 않음 (Cold Data):** 5분이 지나면 아예 삭제(Expire)되어 메모리 낭비를 막는다.

* **장애 대비:** 만약 DB 장애로 Refresh가 실패하더라도, Expire 시간(5분) 전까지는 기존 캐시 데이터를 반환하여 서비스가 셧다운되는 것을 방지할 수 있다.

> **주의:** `refreshAfterWrite`만 사용하면, 오랫동안 조회되지 않는 데이터가 힙 메모리에 계속 남아있을 수 있다(Eviction 트리거가 없으므로). 따라서 메모리 관리를 위해 `expireAfterWrite`나 `maximumSize`를 함께 사용하는 것이 좋다.

## [출처 및 참고]
* [https://github.com/ben-manes/caffeine/wiki/Eviction#time-based](https://github.com/ben-manes/caffeine/wiki/Eviction#time-based)
* [https://github.com/ben-manes/caffeine/wiki/Refresh](https://github.com/ben-manes/caffeine/wiki/Refresh)
* [https://www.javadoc.io/doc/com.github.ben-manes.caffeine/caffeine/latest/com/github/benmanes/caffeine/cache/Caffeine.html](https://www.javadoc.io/doc/com.github.ben-manes.caffeine/caffeine/latest/com/github/benmanes/caffeine/cache/Caffeine.html)
* [https://github.com/ben-manes/caffeine/wiki/Design](https://github.com/ben-manes/caffeine/wiki/Design)
