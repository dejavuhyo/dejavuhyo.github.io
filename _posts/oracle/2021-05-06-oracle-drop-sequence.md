---
title: Oracle DROP SEQUENCE
author: dejavuhyo
date: 2021-05-06 11:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-drop-sequence, drop-sequence, 오라클-drop-sequence, 오라클-시퀀스-삭제]
---

## 1. DROP SEQUENCE 개요
DROP SEQUENCE 문을 사용하여 데이터베이스에서 시퀀스를 제거할 수 있다.

다음은 DROP SEQUENCE 문의 기본 구문이다.

```sql
DROP SEQUENCE schema_name.sequence_name;
```

이 구문에서는 DROP SEQUENCE 키워드 뒤에 제거할 시퀀스의 이름을 지정한다.

시퀀스가 속하는 스키마를 지정하지 않으면 Oracle은 사용자 자신의 스키마에서 시퀀스를 제거한다.

제거하는 시퀀스는 사용자 고유의 스키마에 있거나 스키마 DROP ANY SEQUENCE에서 시퀀스를 제거할 수 있는 시스템 권한이 있어야 한다.

DROP SEQUENCE 문은 시퀀스를 다시 시작하려는 경우에도 유용하다.

예를 들어 현재 값이 100인 시퀀스가 있고 값이 50인 시퀀스를 다시 시작하려는 경우 시퀀스를 삭제하고 START VALUE 50으로 다시 생성할 수 있다.

## 2. DROP SEQUENCE 예제
이 명령문은 no_seq라는 새 시퀀스를 생성한다.

```sql
CREATE SEQUENCE no_seq;
```

데이터베이스에서 no_seq를 제거하려면 다음 문을 사용한다.

```sql
DROP SEQUENCE no_seq;
```

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-sequence/oracle-alter-sequence/](https://www.oracletutorial.com/oracle-sequence/oracle-alter-sequence/)
