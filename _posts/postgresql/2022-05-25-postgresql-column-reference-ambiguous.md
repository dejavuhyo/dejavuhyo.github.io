---
title: PostgreSQL Column reference is ambiguous 해결 방법
author: dejavuhyo
date: 2022-05-25 13:05:00 +0900
categories: [Database, PostgreSQL]
tags: [postgresql-column-reference, column-reference, column-reference-ambiguous, postgresql-컬럼-참조, 컬럼-참조]
---

## 1. 원인
A와 B 테이블을 조인하였을 때 해당 컬럼이 양쪽 테이블에 존재하고 어떤 테이블의 컬럼인지 명시가 되어 있지 않아 발생하는 오류이다.

```sql
select a.id
     , a.name
from a_table a
         join b_table b
              on a.table_name = b.tablename
where id = 'A';
```

## 2. 해결 방법
where 조건에서 id가 어느 테이블의 컬럼인지 명시해 준다.

```sql
select a.id
     , a.name
from a_table a
         join b_table b
              on a.table_name = b.tablename
where a.id = 'A';
```

## [출처 및 참고]
* <https://bono915.tistory.com/entry/Postgresql-ERROR-column-reference-columnname-is-ambiguous-%EC%98%A4%EB%A5%98-%EB%B0%8F-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95>
