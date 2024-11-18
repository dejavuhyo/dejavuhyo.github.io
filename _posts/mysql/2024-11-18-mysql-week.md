---
title: MySQL 주별 통계 WEEK
author: dejavuhyo
date: 2024-11-18 10:20:00 +0900
categories: [Database, MySQL]
tags: [mysql-week, week, mysql-주별-통계, 주별-통계]
---

## 1. 주별 통계
WEEK 함수를 이용한다.

### 1) `ECOLI_DATA` 테이블

| ID | SIZE_OF_COLONY | DIFFERENTIATION_DATE |
|:-----:|:-----:|:-----:|
| 1 | 10 | 2019/01/01 |
| 2 | 2 | 2019/05/01 |
| 3 | 100 | 2020/01/01 |
| 4 | 17 | 2022/04/01 |
| 5 | 10 | 2020/09/01 |
| 6 | 101 | 2021/12/01 |

### 2) SQL

```sql
select CONCAT(WEEK(DIFFERENTIATION_DATE, 7), 'W') as WEEK
     , COUNT(SUB_CD)                              as ECOLI_COUNT
from ECOLI_DATA
group by WEEK(DIFFERENTIATION_DATE)
```

## [출처 및 참고]
* [https://soobindeveloper8.tistory.com/932](https://soobindeveloper8.tistory.com/932)
