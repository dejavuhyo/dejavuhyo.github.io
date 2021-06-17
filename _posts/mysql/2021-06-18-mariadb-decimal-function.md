---
title: MariaDB 소수점 함수
date: 2021-06-18 06:00:00 +0900
categories: [Database, MySQL]
tags: [mariadb-decimal-function, decimal-function, mariadb-ceiling, mariadb-floor, mariadb-round, mariadb-truncate, mariadb-소수점-함수, 소수점-함수, mariadb-절대값, mariadb-올림, mariadb-버림, mariadb-반올림]
---

## 1. ABS (절대값)
절댓값을 만들어 주는 함수

```sql
SELECT ABS(-15.123) AS RESULT_ABS
```

## 2. CEILING (소수점 올림)
소수점을 모두 올림

```sql
SELECT CEILING(15.423) AS RESULT_CEILING
```

## 3. FLOOR (소수점 버림)
소수점을 모두 버림, 음수일 경우 내림

```sql
SELECT FLOOR(15.423) AS RESULT_FLOOR_01,  FLOOR(-15.423) AS RESULT_FLOOR_02
```

## 4. ROUND (소수점 반올림)
소수점 이하 자릿수를 남기고 반올림

```sql
SELECT ROUND(15.423, 2) AS RESULT_ROUND_01, ROUND(15.1257, 3) AS RESULT_ROUND_02
```

## 5. TRUNCATE (소수점 자릿수 버림)
소수점 이하 자릿수를 남기고 버림

```sql
SELECT TRUNCATE(15.423, 2) AS RESULT_TRUNCATE
```

## [출처 및 참고]
* <https://m2seo.tistory.com/27>
