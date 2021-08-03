---
title: MariaDB 이전행, 다음행 조회
date: 2021-08-04 05:00:00 +0900
categories: [Database, MySQL]
tags: [mariadb-lag-lead, lag-lead, mariadb-leg, mariadb-lead, mariadb-이전행-다음행, mariadb-이전행, mariadb-다음행]
---

## 1. LAG

### 1) Syntax

```sql
LAG (expr[, offset]) OVER (
    [ PARTITION BY partition_expression ]
    < ORDER BY order_list >
)
```

### 2) 설명
LAG 함수는 자체 조인 없이 ORDER BY 절에 따라 이전 행의 데이터에 액세스한다. 특정 행은 사용할 현재 행 뒤에 있는 행 수를 지정하는 오프셋(기본값 1)에 의해 결정된다. 오프셋 0은 현재 행이다.

### 3) Example

* 샘플 테이블

```sql
CREATE TABLE t1 (pk int primary key, a int, b int, c char(10), d decimal(10, 3), e real);

INSERT INTO t1 VALUES
    ( 1, 0, 1,    'one',    0.1,  0.001),
    ( 2, 0, 2,    'two',    0.2,  0.002),
    ( 3, 0, 3,    'three',  0.3,  0.003),
    ( 4, 1, 2,    'three',  0.4,  0.004),
    ( 5, 1, 1,    'two',    0.5,  0.005),
    ( 6, 1, 1,    'one',    0.6,  0.006),
    ( 7, 2, NULL, 'n_one',  0.5,  0.007),
    ( 8, 2, 1,    'n_two',  NULL, 0.008),
    ( 9, 2, 2,    NULL,     0.7,  0.009),
    (10, 2, 0,    'n_four', 0.8,  0.010),
    (11, 2, 10,   NULL,     0.9,  NULL);
```

* 조회

```sql
SELECT
    pk,
    LAG(pk) OVER (ORDER BY pk) AS l,
    LAG(pk,1) OVER (ORDER BY pk) AS l1,
    LAG(pk,2) OVER (ORDER BY pk) AS l2,
    LAG(pk,0) OVER (ORDER BY pk) AS l0,
    LAG(pk,-1) OVER (ORDER BY pk) AS lm1,
    LAG(pk,-2) OVER (ORDER BY pk) AS lm2
FROM t1;
```

* 결과

```text
+----+------+------+------+------+------+------+
| pk | l    | l1   | l2   | l0   | lm1  | lm2  |
+----+------+------+------+------+------+------+
|  1 | NULL | NULL | NULL |    1 |    2 |    3 |
|  2 |    1 |    1 | NULL |    2 |    3 |    4 |
|  3 |    2 |    2 |    1 |    3 |    4 |    5 |
|  4 |    3 |    3 |    2 |    4 |    5 |    6 |
|  5 |    4 |    4 |    3 |    5 |    6 |    7 |
|  6 |    5 |    5 |    4 |    6 |    7 |    8 |
|  7 |    6 |    6 |    5 |    7 |    8 |    9 |
|  8 |    7 |    7 |    6 |    8 |    9 |   10 |
|  9 |    8 |    8 |    7 |    9 |   10 |   11 |
| 10 |    9 |    9 |    8 |   10 |   11 | NULL |
| 11 |   10 |   10 |    9 |   11 | NULL | NULL |
+----+------+------+------+------+------+------+
```

## 2. LEAD

### 1) Syntax

```sql
LEAD (expr[, offset]) OVER (
    [ PARTITION BY partition_expression ]
    [ ORDER BY order_list ]
)
```

### 2) 설명
LEAD 함수는 자체 조인 없이 동일한 결과 집합의 다음 행에서 데이터에 액세스한다. 특정 행은 사용할 현재 행 앞의 행 수를 지정하는 오프셋(기본값 1)에 의해 결정된다. 오프셋 0은 현재 행이다.

### 3) Examples

* 샘플 테이블

```sql
CREATE TABLE t1 (pk int primary key, a int, b int, c char(10), d decimal(10, 3), e real);

INSERT INTO t1 VALUES
    ( 1, 0, 1,    'one',    0.1,  0.001),
    ( 2, 0, 2,    'two',    0.2,  0.002),
    ( 3, 0, 3,    'three',  0.3,  0.003),
    ( 4, 1, 2,    'three',  0.4,  0.004),
    ( 5, 1, 1,    'two',    0.5,  0.005),
    ( 6, 1, 1,    'one',    0.6,  0.006),
    ( 7, 2, NULL, 'n_one',  0.5,  0.007),
    ( 8, 2, 1,    'n_two',  NULL, 0.008),
    ( 9, 2, 2,    NULL,     0.7,  0.009),
    (10, 2, 0,    'n_four', 0.8,  0.010),
    (11, 2, 10,   NULL,     0.9,  NULL);
```

* 조회

```sql
SELECT
    pk,
    LEAD(pk) OVER (ORDER BY pk) AS l,
    LEAD(pk,1) OVER (ORDER BY pk) AS l1,
    LEAD(pk,2) OVER (ORDER BY pk) AS l2,
    LEAD(pk,0) OVER (ORDER BY pk) AS l0,
    LEAD(pk,-1) OVER (ORDER BY pk) AS lm1,
    LEAD(pk,-2) OVER (ORDER BY pk) AS lm2
FROM t1;
```

* 결과

```text
+----+------+------+------+------+------+------+
| pk | l    | l1   | l2   | l0   | lm1  | lm2  |
+----+------+------+------+------+------+------+
|  1 |    2 |    2 |    3 |    1 | NULL | NULL |
|  2 |    3 |    3 |    4 |    2 |    1 | NULL |
|  3 |    4 |    4 |    5 |    3 |    2 |    1 |
|  4 |    5 |    5 |    6 |    4 |    3 |    2 |
|  5 |    6 |    6 |    7 |    5 |    4 |    3 |
|  6 |    7 |    7 |    8 |    6 |    5 |    4 |
|  7 |    8 |    8 |    9 |    7 |    6 |    5 |
|  8 |    9 |    9 |   10 |    8 |    7 |    6 |
|  9 |   10 |   10 |   11 |    9 |    8 |    7 |
| 10 |   11 |   11 | NULL |   10 |    9 |    8 |
| 11 | NULL | NULL | NULL |   11 |   10 |    9 |
+----+------+------+------+------+------+------+
```

## [출처 및 참고]
* <https://mariadb.com/kb/en/lag/>
* <https://mariadb.com/kb/en/lead/>
