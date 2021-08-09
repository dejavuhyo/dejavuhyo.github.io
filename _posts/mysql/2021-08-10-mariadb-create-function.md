---
title: MariaDB 함수 생성
date: 2021-08-10 05:30:00 +0900
categories: [Database, MySQL]
tags: [mariadb-create-function, create-function, mariadb-함수-생성, 함수-생성]
---

## 1. Syntax

```sql
CREATE [OR REPLACE]
    [DEFINER = {user | CURRENT_USER | role | CURRENT_ROLE }]
    [AGGREGATE] FUNCTION [IF NOT EXISTS] func_name ([func_parameter[,...]])
    RETURNS type
    [characteristic ...]
    RETURN func_body


func_parameter:
    param_name type


type:
    Any valid MariaDB data type


characteristic:
    LANGUAGE SQL
  | [NOT] DETERMINISTIC
  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
  | COMMENT 'string'


func_body:
    Valid SQL procedure statement
```

## 2. 활용
코드명 구하는 함수

* 코드 데이블

```sql
create table sy_code (
    CODE_GROUP_ID      varchar(20)                            not null comment '코드그룹ID',
    CODE_ID            varchar(20)                            not null comment '코드ID',
    CODE_NM            varchar(100)                           null comment '코드명',
    CODE_DC            varchar(4000)                          null comment '코드설명',
    USE_AT             varchar(1) default 'Y'                 not null comment '사용여부',
    primary key (CODE_GROUP_ID, CODE_ID)
)
comment '코드그룹';

INSERT INTO sy_code (CODE_GROUP_ID, CODE_ID, CODE_NM, USE_AT) VALUES ('APPLY_STTUS', '100', '신규', 'Y');
INSERT INTO sy_code (CODE_GROUP_ID, CODE_ID, CODE_NM, USE_AT) VALUES ('APPLY_STTUS', '200', '변경', 'Y');
INSERT INTO sy_code (CODE_GROUP_ID, CODE_ID, CODE_NM, USE_AT) VALUES ('APPLY_STTUS', '300', '해지', 'Y');
```

* 함수

```sql
CREATE
    FUNCTION FN_CODE_NM(AS_CODE_GROUP_ID VARCHAR(20), AS_CODE_ID VARCHAR(20)) RETURNS VARCHAR(100)
BEGIN
    -- AS_CODE_GROUP_ID : 코드그룹ID
    -- AS_CODE_ID : 코드ID
    DECLARE RTN_VAL VARCHAR(100);

    SELECT
        CODE_NM
        INTO RTN_VAL
    FROM
        sy_code
    WHERE CODE_GROUP_ID = AS_CODE_GROUP_ID
    AND CODE_ID = AS_CODE_ID
    LIMIT 1;

    -- RTN_VAL : 코드명(CODE_NM)
    RETURN RTN_VAL;
END;
```

* SQL

```sql
SELECT
    CODE_ID
    , FN_CODE_NM('APPLY_STTUS', CODE_ID) AS CODE_NM
FROM YOUR_TABLE
```

## [출처 및 참고]
* <https://mariadb.com/kb/en/create-function/>
