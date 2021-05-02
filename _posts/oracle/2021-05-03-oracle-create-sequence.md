---
title: Oracle CREATE SEQUENCE
author: dejavuhyo
date: 2021-05-03 06:20:00 +0900
categories: [Database, Oracle]
tags: [oracle-create-sequence, create-sequence, 오라클-create-sequence, 오라클-시퀀스-생성]
---

## 1. CREATE SEQUENCE 문 소개
CREATE SEQUENCE 문을 사용하여 데이터베이스에 새 시퀀스를 작성할 수 있다.

다음은 CREATE SEQUENCE 문의 기본 구문입니다.

```sql
CREATE SEQUENCE schema_name.sequence_name
[INCREMENT BY interval]
[START WITH first_number]
[MAXVALUE max_value | NOMAXVALUE]
[MINVALUE min_value | NOMINVALUE]
[CYCLE | NOCYCLE]
[CACHE cache_size | NOCACHE]
[ORDER | NOORDER];
```

### 1) CREATE SEQUENCE
CREATE SEQUENCE 키워드 뒤에 시퀀스 이름을 지정한다. 특정 스키마에서 시퀀스를 만들려면 스키마 이름과 시퀀스 이름을 함께 지정할 수 있다.

### 2) INCREMENT BY
INCREMENT BY 키워드 뒤에 시퀀스 번호 간격을 지정한다.

interval은 28자리 미만이어야 한다. 또한 MAXVALUE - MINVALUE보다 작아야 한다.

interval이 양수인 경우 순서는 오름차순이다(예: 1, 2, 3, …).

interval이 음수인 경우 시퀀스는 내림차순이다(예: -1, -2, -3 …).

interval 기본값은 1이다.

### 3) START WITH
시퀀스의 첫 번째 숫자를 지정한다.

첫 번째 숫자의 기본값은 오름차순의 최소값과 내림차순의 최대값이다.

### 4) MAXVALUE
시퀀스의 최대값을 지정한다.

max_value는 START WITH 키워드 뒤에 지정한 first_number보다 크거나 같아야 한다.

### 5) NOMAXVALUE
NOMAXVALUE를 사용하여 오름차순의 경우 최대 값 10^27을 나타내고 내림차순의 경우 -1을 나타낸다. Oracle은 이 옵션을 기본값으로 사용한다.

### 6) MINVALUE
시퀀스의 최소값을 지정힌다.

min_value는 first_number보다 작거나 같아야 하며 max_value보다 작아야 한다.

### 7) NOMINVALUE
NOMINVALUE를 사용하여 오름차순의 경우 최소값 1을 나타내고 내림차순의 경우 -10^26을 나타낸다. 이것이 기본값이다.

### 8) CYCLE
CYCLE을 사용하여 한계값에 도달한 후 시퀀스가 값을 생성하고, 하강 시퀀스의 최소값 및 상승 시퀀스의 최대값을 생성할 수 있다.

오름차순 시퀀스가 최대값까지 도달하면 최소값이 생성된다.

반면에 하강 시퀀스가 최소값에 도달하면 최대값을 생성한다.

### 9) NOCYCLE
다음 값이 한계에 도달할 때 시퀀스가 다음 값 생성을 중지하도록 하려면 NOCYCLE을 사용한다. 이것이 기본값이다.

### 10) CACHE
Oracle이 더 빠른 액세스를 위해 미리 할당하고 메모리에 보관할 시퀀스 값의 수를 지정한다.

캐시 최소 크기는 2이다. 캐시 크기의 최대값은 다음 공식을 기반으로 한다.

```text
(CEIL (MAXVALUE - MINVALUE)) / ABS (INCREMENT)
```

시스템 오류 이벤트가 발생하면, 커밋된 SQL 문에 사용되지 않은 모든 캐시된 시퀀스 값이 손실된다.

### 11) ORDER
ORDER를 사용하여 Oracle이 요청 순서대로 시퀀스 번호를 생성하는지 확인한다.

이 옵션은 Oracle Real Application Cluster를 사용하는 경우에 유용하다. 독점 모드를 사용하는 경우 Oracle은 항상 순서대로 시퀀스 번호를 생성한다.

### 12) NOORDER
Oracle에서 요청 순서대로 시퀀스 번호를 생성하지 않으려면 NOORDER를 사용한다. 이 옵션이 기본값이다.

## 2. CREATE SEQUENCE 문 예제
시퀀스를 사용하는 예제이다.

### 1) 기본 Oracle 시퀀스 예제
다음 명령문은 id_seq라는 오름차순을 생성하는데, 10에서 시작하여 10, 최소값 10, 최대값 100까지 증가시킨다. CYCLE 옵션 때문에 100에 도달하면 10을 반환한다.

```sql
CREATE SEQUENCE id_seq
    INCREMENT BY 10
    START WITH 10
    MINVALUE 10
    MAXVALUE 100
    CYCLE
    CACHE 2;
```

시퀀스의 다음 값을 가져오려면 NEXTVAL pseudo-column을 사용한다.

```sql
SELECT
    id_seq.NEXTVAL
FROM
    dual;
```

결과이다.

```text
NEXTVAL
-------
     10
```

시퀀스의 현재 값을 가져오려면 CURRVAL pseudo-column을 사용한다.

```sql
SELECT
    id_seq.CURRVAL
FROM
    dual;
```

현재 값은 10이다.

```text
CURRVAL
-------
     10
```

이 SELECT 문은 id_seq.NEXTVAL을 반복으로 사용한다.

```sql
SELECT
    id_seq.NEXTVAL
FROM 
    dual
CONNECT BY level <= 9;
```

결과이다.

```text
NEXTVAL
------
    20
    30
    40
    50
    60
    70
    80
    90
   100

9 rows selected
```

id_seq 시퀀스에 대한 CYCLE 옵션을 설정하기 때문에, id_seq의 다음 값은 10이 된다.

```sql
SELECT id_seq.NEXTVAL FROM dual;
```

결과이다.

```text
NEXTVAL
-------
     10
```

### 2) 테이블 컬럼의 시퀀스 사용 예제
Oracle 12c 이전에서는 삽입 시간에만 테이블 컬럼에 시퀀스를 간접적으로 연결할 수 있다.

다음 예를 참조한다.

* __첫째:__ 태스크라는 새 테이블을 생성한다.

```sql
CREATE TABLE tasks(
    id NUMBER PRIMARY KEY,
    title VARCHAR2(255) NOT NULL
);
```

* __둘째:__ 태스크 테이블의 ID 컬럼에 대한 시퀀스를 생성한다.

```sql
CREATE SEQUENCE task_id_seq;
```

* __셋째:__ 작업 테이블에 데이터를 삽입한다.

```sql
INSERT INTO tasks(id, title)
VALUES(task_id_seq.NEXTVAL, 'Create Sequence in Oracle');

INSERT INTO tasks(id, title)
VALUES(task_id_seq.NEXTVAL, 'Examine Sequence Values');
```

* __넷째:__ 작업 테이블에서 데이터를 조회한다.

```sql
SELECT
    id, title
FROM
    tasks;
```

![create-sequence-example](/assets/img/2021-05-03-oracle-create-sequence/create-sequence-example.png)

이 예에서 tasks 테이블은 task_id_seq 순서와 직접 연결되지 않는다.

### 3) ID 컬럼 예제를 통해 시퀀스 사용
오라클 12c에서는 ID 컬럼을 통해 시퀀스를 테이블 컬럼에 연결할 수 있다.

* __첫째:__ 태스크 테이블을 삭제한다.

```sql
DROP TABLE tasks;
```

* __둘째:__ ID 컬럼에 대한 ID 컬럼을 사용하여 태스크 테이블을 재생성한다.

```sql
CREATE TABLE tasks(
    id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    title VARCHAR2(255) NOT NULL
);
```

뒤에서 Oracle은 tasks 테이블의 id 컬럼과 연결되는 시퀀스를 생성한다.

Oracle이 id 컬럼에 대해 시퀀스를 자동으로 생성했기 때문에 Oracle 인스턴스에서 시퀀스 이름이 다를 수 있습니다.

![identity-column](/assets/img/2021-05-03-oracle-create-sequence/identity-column.png)

Oracle은 sys.idnseq$를 사용하여 테이블과 시퀀스 사이의 링크를 저장한다.

이 쿼리는 tasks 테이블과 ISEQ$$_74366 시퀀스의 연결을 반환한다.

```sql
SELECT
    a.name AS table_name,
    b.name AS sequence_name
FROM
    sys.idnseq$ c
    JOIN obj$ a ON c.obj# = a.obj#
    JOIN obj$ b ON c.seqobj# = b.obj#
WHERE
    a.name = 'TASKS';
```

* __셋째:__ 작업 테이블에 일부 행을 삽입한다.

```sql
INSERT INTO tasks(title)
VALUES('Learn Oracle identity column in 12c');

INSERT INTO tasks(title)
VALUES('Verify contents of the tasks table');
```

* __넷째:__ 작업 테이블에서 데이터를 조회한다.

```sql
SELECT
    id, title
FROM
    tasks;
```

![oracle-identity-column-example](/assets/img/2021-05-03-oracle-create-sequence/oracle-identity-column-example.png)

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-sequence/oracle-create-sequence/>
