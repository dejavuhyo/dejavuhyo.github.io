---
title: Oracle MERGE
author: dejavuhyo
date: 2021-03-17 06:10:00 +0900
categories: [Database, Oracle]
tags: [oracle-merge, oracle-merge-into, merge-into, merge, update-insert, 오라클-merge, 오라클-merge-into]
---

## 1. MERGE란
MERGE 명령문을 사용하여 하나 이상의 소스에서 행을 선택하여 업데이트하거나 테이블 또는 뷰에 삽입한다. 조건을 지정하여 대상 테이블 또는 뷰에 업데이트하거나 삽입할지 여부를 결정할 수 있다.

이 문은 여러 작업을 결합하는 편리한 방법이다. 여러 개의 INSERT, UPDATE 및 DELETE DML 문을 방지할 수 있다.

MERGE는 결정론적 문이다. 동일한 MERGE 명령문에서 대상 테이블의 동일한 행을 여러 번 업데이트 할 수 없다.

> Oracle Database는 MERGE 문 중에 세분화된 액세스 제어를 구현하지 않는다. 대상 테이블 또는 테이블에서 세분화된 액세스 제어 기능을 사용하는 경우 MERGE 대신 동등한 INSERT 및 UPDATE 문을 사용하여 오류 메시지를 방지하고 올바른 액세스 제어를 보장한다.

## 2. 문법

### 1) MERGE

![merge](/assets/img/2021-03-17-oracle-merge-into/merge.gif)

```sql
MERGE [ hint ]
    INTO [ schema. ] { table | view } [ t_alias ]
    USING { [ schema. ] { table | view }
        | subquery 
        } [ t_alias ]
    ON ( condition )
    [ merge_update_clause ]
    [ merge_insert_clause ]
    [ error_logging_clause ]
```

### 2) MERGE UPDATE CLAUSE

![merge-update-clause](/assets/img/2021-03-17-oracle-merge-into/merge-update-clause.gif)

```sql
WHEN MATCHED THEN
UPDATE SET column = { expr | DEFAULT }
        [, column = { expr | DEFAULT } ]...
[ where_clause ]
[ DELETE where_clause ]
```

### 3) MERGE INSERT CLAUSE

![merge-insert-clause](/assets/img/2021-03-17-oracle-merge-into/merge-insert-clause.gif)

```sql
WHEN NOT MATCHED THEN
INSERT [ (column [, column ]...) ]
VALUES ({ expr [, expr ]... | DEFAULT })
[ where_clause ]
```

### 4) WHERE CLAUSE

![where-clause](/assets/img/2021-03-17-oracle-merge-into/where-clause.gif)

```sql
WHERE condition
```

### 5) ERROR LOGGING CLAUSE

![error-logging-clause](/assets/img/2021-03-17-oracle-merge-into/error-logging-clause.gif)

```sql
LOG ERRORS 
    [ INTO [schema.] table ]
    [ (simple_expression) ]
    [ REJECT LIMIT { integer | UNLIMITED } ]
```

## 3. 활용

### 1) 동일한 테이블 구조
동일한 테이블 구조로 되어 있는 TMP_SCORE 테이블로부터 데이터를 옮기는 예이다.

```sql
MERGE INTO TB_SCORE S
    USING TMP_SCORE T
        ON (S.COURSE_ID = T.COURSE_ID AND S.STUDENT_ID = T.STUDENT_ID)
    WHEN MATCHED THEN
        UPDATE SET S.SCORE = T.SCORE
    WHEN NOT MATCHED THEN
        INSERT (S.COURSE_ID, S.STUDENT_ID, S.SCORE) 
        VALUES (T.COURSE_ID, T.STUDENT_ID, T.SCORE)
```

### 2) SELECT 이용

```sql
MERGE INTO CUSTOMER C
    USING (
        SELECT USERNO, USERNAME, ADDRESS, PHONE FROM NEW_JOIN
        WHERE INPUT_DATE = '20170724') N
    ON (C.USERNO = N.USERNO)
    WHEN MATCHED THEN
        UPDATE SET C.USERNAME = N.USERNAME, C.ADDRESS = N.ADDRESS, C.PHONE = N.PHONE
    WHEN NOT MATCHED THEN
        INSERT (USERNO, USERNAME, ADDRESS, PHONE)
        VALUES (N.USERNO, N.USERNAME, N.ADDRESS, N.PHONE)
 ```

```sql
MERGE INTO bonuses D
    USING (
        SELECT employee_id, salary, department_id FROM employees
        WHERE department_id = 80) S
    ON (D.employee_id = S.employee_id)
    WHEN MATCHED THEN
        UPDATE SET D.bonus = D.bonus + S.salary*.01
        DELETE WHERE (S.salary > 8000)
    WHEN NOT MATCHED THEN
        INSERT (D.employee_id, D.bonus)
        VALUES (S.employee_id, S.salary*.01)
        WHERE (S.salary <= 8000)
```

### 3) 직접 값을 입력
다른 테이블에서 데이터를 비교하여 가져오는 것이 아니라, 직접 값을 입력한다면 DUAL을 사용한다.

```sql
MERGE INTO TB_SCORE S
    USING DUAL
        ON (S.COURSE_ID = 'C1' AND S.STUDENT_ID = 'S1')
    WHEN MATCHED THEN
        UPDATE SET S.SCORE = 20
    WHEN NOT MATCHED THEN
        INSERT (S.COURSE_ID, S.STUDENT_ID, S.SCORE)
        VALUES ('C1', 'S1', 20)
```

### 4) UPDATE 혹은 INSERT 하나만 수행

```sql
MERGE INTO TB_SCORE S
    USING DUAL
        ON (S.COURSE_ID = 'C1' AND S.STUDENT_ID = 'S1')
    WHEN NOT MATCHED THEN
        INSERT (S.COURSE_ID, S.STUDENT_ID, S.SCORE)
        VALUES ('C1', 'S1', 20)
```

## [출처 및 참고]
* <https://offbyone.tistory.com/253>
* <https://docs.oracle.com/cd/B28359_01/server.111/b28286/statements_9016.htm#SQLRF01606>
* <https://hello-nanam.tistory.com/30>
