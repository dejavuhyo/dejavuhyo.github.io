---
title: 중복 레코드 개수 확인
author: dejavuhyo
date: 2021-02-16 06:30:00 +0900
categories: [Database, SQL]
tags: [duplicate-records, duplicate-records-count, duplicate]
---

## 1. 테이블

```sql
sql> SELECT id, str FROM str_table;

+----+-----+
| id | str |
+----+-----+
|  1 | AAA |
|  2 | BBB |
|  3 | CCC |
|  4 | AAA |
|  5 | CCC |
|  6 | DDD |
|  7 | EEE |
|  8 | AAA |
+----+-----+
```

## 2. 모든 레코드 개수
`SELECT 컬럼명, COUNT(*) FROM 테이블명 GROUP BY 컬럼명;`

```sql
sql> SELECT str, COUNT(*) FROM str_table GROUP BY str;

+-----+----------+
| str | COUNT(*) |
+-----+----------+
| AAA |        3 |
| BBB |        1 |
| CCC |        2 |
| DDD |        1 |
| EEE |        1 |
+-----+----------+
```

## 3. 중복 레코드 개수
`SELECT 컬럼명, COUNT(*) FROM 테이블명 GROUP BY 컬럼명 HAVING COUNT(*) > 1;`

```sql
sql> SELECT str, COUNT(*) FROM str_table GROUP BY str HAVING COUNT(*) > 1;

+-----+----------+
| str | COUNT(*) |
+-----+----------+
| AAA |        3 |
| CCC |        2 |  
+-----+----------+
```

## [출처 및 참고]
* [https://zetawiki.com/wiki/DB_%EC%A4%91%EB%B3%B5_%EA%B0%9C%EC%88%98_%ED%99%95%EC%9D%B8](https://zetawiki.com/wiki/DB_%EC%A4%91%EB%B3%B5_%EA%B0%9C%EC%88%98_%ED%99%95%EC%9D%B8)
