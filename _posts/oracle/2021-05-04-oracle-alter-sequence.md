---
title: Oracle ALTER SEQUENCE
author: dejavuhyo
date: 2021-05-04 06:20:00 +0900
categories: [Database, Oracle]
tags: [oracle-alter-sequence, alter-sequence, 오라클-alter-sequence, 오라클-시퀀스-수정]
---

## 1. ALTER SEQUENCE 개요
ALTER SEQUENCE 문을 사용하면 시퀀스 개체의 증분, 최소값, 최대값, 캐시된 숫자 및 동작을 변경할 수 있다.

다음은 ALTER SEQUENCE 문의 기본 구문이다.

```sql
ALTER SEQUENCE schema_name.sequence_name
    [INCREMENT BY interval]
    [MAXVALUE max_value | NOMAXVALUE]
    [MINVALUE min_value | NOMINVALUE]
    [CYCLE | NOCYCLE]
    [CACHE cache_size | NOCACHE]
    [ORDER | NOORDER];
```

모든 파라미터는 [CREATE SEQUENCE 문](https://dejavuhyo.github.io/posts/oracle-create-sequence/)에 설명된 것과 동일한 의미를 가진다.

시퀀스 속성을 변경하면 Oracle이 백그라운드에서 몇 가지 검증을 수행한다. 예를 들어 시퀀스의 최대 수를 현재 시퀀스 번호보다 작은 값으로 변경하면 Oracle에서 오류가 발생한다.

다른 번호로 시퀀스를 변경하려면 다음과 같이 삭제하고 다시 만들어야 한다.

```sql
DROP SEQUENCE schema_name.sequence_name;

CREATE SEQUENCE schema_name.sequence_name
    START WITH new_value;
```

ALTER SEQUENCE 문은 미래의 시퀀스 번호에만 적용된다.

## 2. ALTER SEQUENCE 예제
다음 명령문은 CREATE SEQUENCE 문을 사용하여 invoice_seq라는 새 시퀀스를 생성한다.

```sql
CREATE SEQUENCE invoice_seq
    START WITH 20190001;
```

이 예에서는 ALTER SEQUENCE 문을 사용하여 invoice_seq 시퀀스에 대한 CACHE를 켠다.

```sql
ALTER SEQUENCE invoice_seq
CACHE 10;
```

START WITH 번호를 변경하려면 먼저 invoice_seq 시퀀스를 삭제한다.

```sql
DROP SEQUENCE invoice_seq;
```

그런 다음 다시 만든다.

```sql
CREATE SEQUENCE invoice_seq
    START WITH 20200001
    CACHE 10;
```

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-sequence/oracle-alter-sequence/](https://www.oracletutorial.com/oracle-sequence/oracle-alter-sequence/)
