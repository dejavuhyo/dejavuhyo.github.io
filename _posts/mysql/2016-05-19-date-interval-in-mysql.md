---
title: MySQL에서 날짜간의 간격 구하기
author: dejavuhyo
date: 2016-05-19 11:13:00 +0900
categories: [Language, Java]
tags: [interval-between-dates, interval-dates, 날짜-간격, mysql-날짜-간격]
---

## 1. 날짜간 간격
MySQL에서 날짜간의 간격을 구하기 위해서 `TO_DAYS` 함수를 사용하면 된다.

`TO_DAY` 함수는 `TO_SECONDS` 함수처럼 지원하는 버전대가 없기 때문에 MySQL 버젼 몇이든 상관 없이 사용할 수 있다.

### 1) 형식

```sql
SELECT TO_DAYS(DATE) - TO_DAYS(DATE);
```

### 2) 남은 일수 계산
2016년 5월 19일에서 2016년 6월 1일까지 남은 일수 계산

```sql
SELECT TO_DAYS('2016-06-01') - TO_DAYS('2016-05-19') AS DAYS;
```

### 3) DATE형으로 변환
CAST 함수를 이용한 DATE형으로 변환하여 사용

```sql
SELECT TO_DAYS(CAST('2016-06-01' AS DATE)) - TO_DAYS(CAST('2016-05-19' AS DATE)) AS DAYS;
```

## [출처 및 참고]
* [www.webmadang.net/database/database.do?action=read&boardid=4003&page=1&seq=26](www.webmadang.net/database/database.do?action=read&boardid=4003&page=1&seq=26)
