---
title: MariaDB 날짜로 요일 구하기
author: dejavuhyo
date: 2021-06-10 06:00:00 +0900
categories: [Database, MySQL]
tags: [mariadb-day-of-week, mariadb-week-day, sql-day-of-week, sql-week-day, mariadb-요일, sql-요일]
---

## 1. DAYOFWEEK 함수 이용
DAYOFWEEK 함수는 일요일은 1, 월요일은 2, 화요일은 3, 수요일은 4, 목요일은 5, 금요일은 6, 토요일은 7을 반환해 준다.

```sql
SELECT (case DAYOFWEEK(now())
    when '1' then '일요일'
    when '2' then '월요일'
    when '3' then '화요일'
    when '4' then '수요일'
    when '5' then '목요일'
    when '6' then '금요일'
    when '7' then '토요일'
    end) as DAY_OF_WEEK
from
    dual
```

## 2. WEEKDAY 함수 이용
WEEKDAY 함수는 월요일은 0, 화요일은 1, 수요일은 2, 목요일은 3, 금요일은 4, 토요일은 5, 일요일은 6을 반환해 준다.

```sql
SELECT (case WEEKDAY(now())
    when '0' then '월요일'
    when '1' then '화요일'
    when '2' then '수요일'
    when '3' then '목요일'
    when '4' then '금요일'
    when '5' then '토요일'
    when '6' then '일요일'
    end) as WEEK_DAY
from
    dual
```

## [출처 및 참고]
* [www.webmadang.net/database/database.do?action=read&boardid=4003&page=1&seq=35](www.webmadang.net/database/database.do?action=read&boardid=4003&page=1&seq=35)
