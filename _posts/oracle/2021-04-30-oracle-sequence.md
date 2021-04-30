---
title: Oracle Sequence
author: dejavuhyo
date: 2021-04-30 10:30:00 +0900
categories: [Database, Oracle]
tags: [oracle-sequence, sequence, 오라클-sequence, 오라클-시퀀스]
---

## 1. 시퀀스란
시퀀스는 순서가 중요한 정수 목록이다. 예를 들어 (1,2,3,4,5)와 (5,4,3,2,1)은 같은 멤버를 가지고 있음에도 불구하고 완전히 다른 순서이다.

## 2. 시퀀스 생성
CREATE SEQUENCE 문을 사용하면 자신의 스키마에 새 시퀀스 개체를 만들 수 있다.

예를 들어, 이 문은 CREATE SEQUENCE 문을 사용하여 item_seq라는 이름의 새 시퀀스 개체를 만든다.

```sql
CREATE SEQUENCE item_seq;
```

시퀀스 개체를 사용하여 고유 정수 시퀀스를 생성할 수 있으며, 대부분 서로게이트 키 컬럼에 사용할 수 있다.

오라클 12c는 테이블의 ID 컬럼과 연결된 시퀀스 개체를 자동으로 생성한다.

## 3. 시퀀스 사용
시퀀스에 사용할 수 있는 다음 값에 액세스하려면 NEXTVAL pseudo-column을 사용한다.

```sql
SELECT
    item_seq.NEXTVAL
FROM
    dual;
```

```text
  NEXTVAL
---------
        1
```

NEXTVAL pseudo-column을 통해 시퀀스 번호를 획득하면 CURRVAL pseudo-column을 사용하여 반복 액세스할 수 있다.

```sql
SELECT
    item_seq.CURRVAL
FROM
    dual;
```

```text
  CURRVAL
---------
        1
```

다음 명령문은 SQL 문에서 item_seq 시퀀스를 반복적으로 사용한다.

```sql
SELECT
    item_seq.NEXTVAL
FROM
    dual
CONNECT BY
    level <= 5;
```

```text
  NEXTVAL
---------
        2
        3
        4
        5
        6
```

이 예에서는 INSERT 문의 item_seq 시퀀스를 사용하여 items 테이블의 item_id 열에 대한 값을 채운다.

```sql
CREATE TABLE items(
    item_id NUMBER
);

INSERT INTO items(item_id) VALUES(item_seq.NEXTVAL);
INSERT INTO items(item_id) VALUES(item_seq.NEXTVAL);

COMMIT;

SELECT
    item_id
FROM
    items;
```

```text
  ITEM_ID
---------
        7
        8
```

Oracle 11g 이후부터는 PL/SQL의 시퀀스를 사용할 수 있다. 이면에서 Oracle은 dual 테이블의 쿼리를 사용하지만 코드를 더욱 깔끔하게 만든다.

```sql
DECLARE
    v_seq NUMBER;
BEGIN
    v_seq := item_seq.NEXTVAL;
    DBMS_OUTPUT.put_line('v_seq=' || v_seq);
END;
```

```text
v_seq=9
```

## 4. 시퀀스 수정
기존 시퀀스 개체의 특성 및 동작을 수정하려면 ALTER SEQUENCE 문을 사용한다.

다음 예에서는 ALTER SEQUENCE를 사용하여 item_seq의 최대값을 100으로 설정한다.

```sql
ALTER SEQUENCE item_seq MAXVALUE 100;
```

## 5. 시퀀스 제거
데이터베이스에서 기존 시퀀스를 제거하려면 DROP SEQUENCE 문을 사용한다.

다음 예에서는 DROP SEQUENCE 문을 사용하여 item_seq 시퀀스를 삭제한다.

```sql
DROP SEQUENCE item_seq;
```

## 6. Oracle 시퀀스 권한
Oracle은 CREATE SEQUENCE 시스템 권한을 제공하여 사용자가 시퀀스를 생성, 변경 및 삭제할 수 있도록 한다.

이 명령문은 사용자에게 CREATE SEQUENCE 권한을 부여한다.

```sql
GRANT CREATE SEQUENCE
TO user_name;
```

또한 Oracle은 기본 제공 스키마를 포함하여 모든 스키마의 시퀀스를 조작할 수 있는 다음과 같은 권한을 제공한다.

* CREATE ANY SEQUENCE

* ALTER ANY SEQUENCE

* DROP ANY SEQUENCE

* SELECT ANY SEQUENCE

따라서 다음 명령을 실행하기 전에 신중하게 고려해야 한다.

```sql
GRANT CREATE ANY SEQUENCE,
    ALTER ANY SEQUENCE,
    DROP ANY SEQUENCE,
    SELECT ANY SEQUENCE
TO user_name;
```

시퀀스 소유자는 시퀀스에 대한 전체 권한을 가진다. 시퀀스에 대한 다른 사용자 액세스 권한을 부여하려면 다음 명령에 표시된 대로 해당 사용자에게 SELECT 개체 권한을 부여할 수 있다.

```sql
GRANT SELECT ON user_name.sequence_name 
TO another_user;
```

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-sequence/>
