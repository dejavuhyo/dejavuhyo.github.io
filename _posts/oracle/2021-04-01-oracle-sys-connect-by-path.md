---
title: Oracle SYS_CONNECT_BY_PATH
author: dejavuhyo
date: 2021-04-01 06:30:00 +0900
categories: [Database, Oracle]
tags: [oracle-sys-connect-by-path, sys-connect-by-path, oracle-hierarchical-function, 오라클-sys-connect-by-path, 오라클-hierarchical-function]
---

## 1. Syntax

![sys-connect-by-path](/assets/img/2021-04-01-oracle-sys-connect-by-path/sys-connect-by-path.gif)

```text
SYS_CONNECT_BY_PATH(column, char)
```

## 2. Purpose
SYS_CONNECT_BY_PATH는 계층적 쿼리에서만 유효하다. 루트부터 노드까지 열 값의 경로를 반환하고, 'CONNECT BY' 조건으로 반환되는 각 행에 대해 열 값을 'char'로 구분한다.

column과 char는 모두 CHAR, VARCAR2, NCHAR 또는 NVARCAR2 데이터 유형 중 하나일 수 있다. 반환된 문자열은 VARCHAR2 데이터 형식이며 column과 동일한 문자 집합에 있다.

## 3. Examples

### 1) 직원 이름의 경로 반환

```sql
SELECT
    LPAD(' ', 2*level-1)||SYS_CONNECT_BY_PATH(last_name, '/') "Path"
FROM
    employees
START WITH
    last_name = 'Kochhar'
CONNECT BY PRIOR
    employee_id = manager_id;
```

* 결과

```text
/Kochhar
    /Kochhar/Greenberg
        /Kochhar/Greenberg/Faviet
        /Kochhar/Greenberg/Chen
        /Kochhar/Greenberg/Sciarra
        /Kochhar/Greenberg/Urman
        /Kochhar/Greenberg/Popp
    /Kochhar/Whalen
    /Kochhar/Mavris
    /Kochhar/Baer
    /Kochhar/Higgins
        /Kochhar/Higgins/Gietz
```

### 2) CLASS_NAME 경로 반환

* 샘플 테이블

| CLASS_CODE | UP_CLASS_CODE | CLASS_LEVEL | CLASS_NAME |
|:---:|:---:|:---:|:---:|
| 10 |  | 1 | 구매일반 |
| 1010 | 10 | 2 | 구매처 |
| 101001 | 1010 | 3 | 구매 |
| 10100101 | 101001 | 4 | 원재료 |
| 1010010101 | 10100101 | 5 | 주재료 |
| 20 |  | 1 | 판매일반 |
| 2010 | 20 | 2 | 판매처 |
| 201001 | 2010 | 3 | 판매 |
| 20100101 | 201001 | 4 | 판매업체 |

* Create SQL

```sql
CREATE TABLE sample_table (
    CLASS_CODE VARCHAR2(30),
    UP_CLASS_CODE VARCHAR2(30),
    CLASS_LEVEL VARCHAR2(30),
    CLASS_NAME VARCHAR2(30)
);

INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('10', '', '1', '구매일반');
INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('1010', '10', '2', '구매처');
INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('101001', '1010', '3', '구매');
INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('10100101', '101001', '4', '원재료');
INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('1010010101', '10100101', '5', '주재료');
INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('20', '', '1', '판매일반');
INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('2010', '20', '2', '판매처');
INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('201001', '2010', '3', '판매');
INSERT INTO sample_table (CLASS_CODE, UP_CLASS_CODE, CLASS_LEVEL, CLASS_NAME) VALUES ('20100101', '201001', '4', '판매업체');
```

* SQL

```sql
SELECT
    CLASS_CODE
    , UP_CLASS_CODE
    , CLASS_LEVEL
    , CLASS_NAME  
    , LTRIM(SYS_CONNECT_BY_PATH(CLASS_NAME,' > '), '> ') AS CLASS_PATH
FROM
    sample_table
START WITH CLASS_LEVEL = '1'
CONNECT BY PRIOR CLASS_CODE = UP_CLASS_CODE
```

* 결과

![sys-connect-by-path-example](/assets/img/2021-04-01-oracle-sys-connect-by-path/sys-connect-by-path-example.png)

## [출처 및 참고]
* <https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions164.htm>
* <http://sqlfiddle.com/>
