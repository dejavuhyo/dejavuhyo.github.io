---
title: PostgreSQL ROWNUM
author: dejavuhyo
date: 2021-02-17 06:10:00 +0900
categories: [Database, PostgreSQL]
tags: [rownum, postgresql-rownum]
---

## 1. 동작 원리
ROWNUM은 쿼리 내에서 사용 가능한(실제 컬럼이 아닌) 가상 컬럼(pseudo column)이다. ROWNUM에는 숫자 1, 2, 3, 4, 5 ... N의 값이 할당된다. 여기서 N은 ROWNUM과 함께 사용하는 로우의 수를 의미한다.

ROWNUM의 값은 ROW에 영구적으로 할당되지 않는다. 테이블의 ROW는 숫자와 연계되어 참조될 수 없다. 따라서 테이블에서 "row 5"를 요청할 수 있는 방법은 없다. "row 5"라는 것은 존재하지 않기 때문이다.

ROWNUM 값이 할당되는 방법은 쿼리의 조건절이 처리되고 난 이후, 그리고 sort, aggregation 이 수행되기 이전에 할당된다. ROWNUM 값은 할당된 이후에만 증가(increment) 된다.

## 2. 활용

```sql
sql> SELECT (row_numver() over()) AS rownum, id, title FROM board;

+--------+----+-------+
| rownum | id | title |
+--------+----+-------+
|      1 | 10 |   AAA |
|      2 | 20 |   BBB |
|      3 | 30 |   CCC |
|      4 | 40 |   DDD |
|      5 | 50 |   EEE |
+--------+----+-------+
```

## [출처 및 참고]
* <https://kdata.or.kr/info/info_04_view.html?field=&keyword=&type=techreport&page=247&dbnum=127365&mode=detail&type=techreport>
* <http://www.devkuma.com/books/pages/860>
