---
title: MariaDB INSERT 데이터 중복 시 UPDATE
author: dejavuhyo
date: 2021-08-18 06:00:00 +0900
categories: [Database, MySQL]
tags: [mariadb-insert-on-duplicate-key-update, insert-on-duplicate-key-update, mariadb-insert-update, insert-update, mariadb-insert-데이터-중복-update, insert-데이터-중복-update]
---

## 1. INSERT ON DUPLICATE KEY UPDATE 구문

```sql
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
  [INTO] tbl_name [PARTITION (partition_list)] [(col,...)]
  {VALUES | VALUE} ({expr | DEFAULT},...),(...),...
  [ ON DUPLICATE KEY UPDATE
    col=expr
      [, col=expr] ... ]
```

혹은

```sql
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name [PARTITION (partition_list)]
    SET col={expr | DEFAULT}, ...
    [ ON DUPLICATE KEY UPDATE
      col=expr
        [, col=expr] ... ]
```

혹은

```sql
INSERT [LOW_PRIORITY | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name [PARTITION (partition_list)] [(col,...)]
    SELECT ...
    [ ON DUPLICATE KEY UPDATE
      col=expr
        [, col=expr] ... ]
```

## 2. 설명
`INSERT ... ON DUPLICATE KEY UPDATE`는 고유한 키 또는 기본 키가 중복된 경우 대신 업데이트를 수행하는 INSERT 문의 MariaDB/MySQL 확장자이다.

API의 `CLIENT_FOUND_ROWS` 플래그가 설정되지 않은 한 행이 삽입되면 row/s 영향 값은 1로 보고되고 행이 업데이트되면 row 2로 보고된다.

둘 이상의 고유 인덱스가 일치하면 첫 번째 인덱스만 업데이트된다. 둘 이상의 고유 인덱스가 있는 테이블에서는 이 문을 사용하지 않는 것이 좋다.

테이블에 `AUTO_INCREMENT` 기본 키가 있고 문이 행을 삽입하거나 업데이트하는 경우 `LAST_INSERT_ID()` 함수는 해당 `AUTO_INCREMENT` 값을 반환한다.

VALUES() 함수는 ON DUPLICATE KEY UPDATE 절에서만 사용할 수 있으며 다른 컨텍스트에서는 의미가 없다. 문의 INSERT 부분에서 열 값을 반환한다. 이 함수는 특히 다중 행 삽입에 유용하다.

ON DUPLICATE KEY UPDATE를 사용할 때는 IGNORE 및 DELAYED 옵션이 무시된다.

## 3. Examples

### 1) 컬럼 한개

테이블 생성

```sql
CREATE TABLE ins_duplicate (id INT PRIMARY KEY, animal VARCHAR(30));
INSERT INTO ins_duplicate VALUES (1,'Aardvark'), (2,'Cheetah'), (3,'Zebra');
```

기존 키가 없는 경우 일반 INSERT로 실행된다.

```sql
INSERT INTO ins_duplicate VALUES (4,'Gorilla') ON DUPLICATE KEY UPDATE animal='Gorilla';
```

```sql
SELECT * FROM ins_duplicate;

+----+----------+
| id | animal   |
+----+----------+
|  1 | Aardvark |
|  2 | Cheetah  |
|  3 | Zebra    |
|  4 | Gorilla  |
+----+----------+
```

기본 키 값이 1인 일반 INSERT는 기존 키 때문에 실패한다.

```sql
INSERT INTO ins_duplicate VALUES (1, 'Antelope');

ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
```

그러나 INSERT ON DUPLICATE KEY UPDATE을 대신 사용할 수 있다.

```sql
INSERT INTO ins_duplicate VALUES (1,'Antelope') ON DUPLICATE KEY UPDATE animal='Antelope';

Query OK, 2 rows affected (0.09 sec)
```

영향을 받는 것으로 보고된 행은 두 개이지만 이는 업데이트만을 나타낸다.

```sql
SELECT * FROM ins_duplicate;

+----+----------+
| id | animal   |
+----+----------+
|  1 | Antelope |
|  2 | Cheetah  |
|  3 | Zebra    |
|  4 | Gorilla  |
+----+----------+
```

### 2) 컬럼 여러개

테이블 조회

```sql
SELECT * FROM ins_duplicate;

+----+----------+------+
| id | animal   | age  |
+----+----------+------+
|  1 | Antelope |   11 |
|  2 | Lion     |   12 |
|  3 | Zebra    |   13 |
|  4 | Gorilla  |   14 |
+----+----------+------+
```

컬럼 여러개 업데이트

```sql
INSERT INTO ins_duplicate (id, animal, age) VALUES (1, 'Antelope', 11)
ON DUPLICATE KEY UPDATE animal = 'Tiger', age = 12;
```

## [출처 및 참고]
* [https://mariadb.com/kb/en/insert-on-duplicate-key-update/](https://mariadb.com/kb/en/insert-on-duplicate-key-update/)
