---
title: MySQL 분기별 통계 QUARTER
author: dejavuhyo
date: 2024-11-15 11:01:00 +0900
categories: [Database, MySQL]
tags: [mysql-quarter, quarter, mysql-분기별, mysql-분기별-통계, 분기별-통계]
---

## 1. 분기별 통계
QUARTER 함수를 이용한다.

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
SELECT CONCAT(QUARTER(DIFFERENTIATION_DATE), 'Q') AS QUARTER
     , COUNT(SIZE_OF_COLONY)                      AS ECOLI_COUNT
FROM ECOLI_DATA
GROUP BY QUARTER
order by QUARTER asc
```

## [출처 및 참고]
* [https://soobindeveloper8.tistory.com/932](https://soobindeveloper8.tistory.com/932)
* [https://school.programmers.co.kr/learn/courses/30/lessons/299308](https://school.programmers.co.kr/learn/courses/30/lessons/299308)
