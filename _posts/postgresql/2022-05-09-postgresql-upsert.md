---
title: PostgreSQL 데이터가 있으면 Update 데이터가 없으면 Insert
author: dejavuhyo
date: 2022-05-09 16:50:00 +0900
categories: [Database, PostgreSQL]
tags: [postgresql-update-insert, update-insert, upsert, on-conflict, 업데이트-인서트, 업서트, postgresql-업서트]
---

## 1. Upsert
오라클은 `merge into`, MySql은 `on duplicate on key update`를 사용하며 PostgreSQL에서는 `insert into ~ on conflict do update` 구문을 사용한다.

## 2. 구문

```sql
INSERT INTO [TABLE] (COLUMN1, COLUMN2, ...)
     VALUES (VALUE1, VALUE2, ...)
     ON CONFLICT ([column_name / ON CONSTRAINT constraint_name/ WHERE predicate])
     [DO NOTHING]
     [DO UPDATE SET column1 = value1, ...]
```

* **ON CONFLICT column_name:** 특정 컬럼명을 기준으로 체크를하며 PK기준 컬럼을 여러개 넣을수 있다.

* **ON CONFLICT ON CONSTRAINT ~:** 테이블 생성시 만든 constraint명을 기준으로 체크한다.

* **ON CONFLICT WHERE predicate:** UNIQUE INDEX 생성 시 사용한다.

ON CONFLICT 사용시 옵션이다.

* **DO NOTHING:** 아무런 동작을 안하도록 설정한다.

* **DO UPDATE SET ~:** UPDATE의 역할이 가능하다.

## 3. 활용

### 1) 특정 컬럼을 기준으로 체크하고 충돌날 경우 아무일도 하지 않음 (DO NOTHING)

```sql
INSERT INTO EMPLOYEE
    (EMP_SN, SALARY, NAME, DEPT, ETC)
VALUES
    (1, 5000, 'mine', 'it', 'record') ON CONFLICT (EMP_SN)
DO NOTHING
```

### 2) 특정 컬럼을 기준으로 체크하고 충돌날 경우 UPDATE

```sql
INSERT INTO EMPLOYEE
    (EMP_SN, SALARY, NAME, DEPT, ETC)
VALUES
    (#{empSn}, #{salary}, #{name}, #{dept}, #{etc})
ON CONFLICT (EMP_SN)
DO UPDATE SET
    EMP_SN = #{empSn}, SALARY = #{salary}, NAME = #{name}, DEPT = #{dept}, ETC = #{etc}
```

### 3) 특정 CONSTRAINT 기준으로 체크하기

```sql
INSERT INTO EMPLOYEE
    (EMP_SN, SALARY, NAME, DEPT, ETC)
VALUES
    (2, 2400, 'mine', 'it', 'record')
ON CONFLICT ON CONSTRAIN
    mine_it_record_key
DO NOTHING
```

> ON CONFLICT절은 PostgreSQL 9.5에서만 사용할 수 있다.

## [출처 및 참고]
* <https://mine-it-record.tistory.com/342>
* <https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-upsert/>
