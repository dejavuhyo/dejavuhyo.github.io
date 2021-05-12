---
title: Oracle UNPIVOT
author: dejavuhyo
date: 2021-05-13 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-unpivot, unpivot, 오라클-unpivot, 오라클-언피벗]
---

## 1. Oracle UNPIVOT
Oracle UNPIVOT 절을 사용하면 열을 행으로 전환할 수 있다. UNPIVOT 절은 PIVOT 절과 반대이다. 단, 전치 과정 동안 데이터의 집계를 해제하지 않는다는 점이다.

![oracle-unpivot](/assets/img/2021-05-13-oracle-unpivot/oracle-unpivot.png)

다음은 Oracle UNPIVOT 절의 구문을 보여 준다.

```sql
SELECT
    select_list
FROM table_name
UNPIVOT [INCLUDE | EXCLUDE NULLS] (
    unpivot_clause
    unpivot_for_clause
    unpivot_in_clause
);
```

이 구문에서는 다음을 수행한다.

* unpivot_clause를 사용하면 지정되지 않은 측정값 값을 나타내는 열의 이름을 지정할 수 있다.

* unpivot_for_clause에서는 측정값의 값을 저장할 각 열의 이름을 지정할 수 있다.

* unpivot_in_clause에는 피벗되지 않을 피벗된 열이 포함되어 있다.

* INCLUDE ```|``` EXCLUDE NULLS 절에서는 null-valued 행을 포함하거나 제외할 수 있다.

* INCLUDE NULLS 절은 Oracle에 null 값 행을 포함하도록 지시한다.

* 반면에 EXPLUCT NULS 절은 반환된 결과 집합에서 null 값 행을 제거한다.

기본적으로 unpivot 작업은 null-valued 행을 제외한다.

## 2. 샘플 테이블 설정

* __첫째:__ sale_stats라는 새 테이블을 만든다.

```sql
CREATE TABLE sale_stats(
    id INT PRIMARY KEY,
    fiscal_year INT,
    product_a INT,
    product_b INT,
    product_c INT
);
```

* __둘째:__ sale_stats 테이블에 일부 행을 삽입한다.

```sql
INSERT INTO sale_stats(id, fiscal_year, product_a, product_b, product_c)
VALUES(1,2017, NULL, 200, 300);

INSERT INTO sale_stats(id, fiscal_year, product_a, product_b, product_c)
VALUES(2,2018, 150, NULL, 250);

INSERT INTO sale_stats(id, fiscal_year, product_a, product_b, product_c)
VALUES(3,2019, 150, 220, NULL);
```

* __셋째:__ sale_stats 테이블에서 데이터를 조회한다.

```sql
SELECT * FROM sale_stats;
```

다음은 결과이다.

![sample-table](/assets/img/2021-05-13-oracle-unpivot/sample-table.png)

## 3. UNPIVOT 예제
이 문에서는 UNPIVOT 절을 사용하여 product_a, product_b 및 product_c 열을 다음과 같은 행으로 회전한다.

```sql
SELECT * FROM sale_stats
UNPIVOT(
    quantity -- unpivot_clause
    FOR product_code -- unpivot_for_clause
    IN ( -- unpivot_in_clause
        product_a AS 'A',
        product_b AS 'B',
        product_c AS 'C'
    )
);
```

이 예에서는 다음을 수행한다.

unpivot_clause는 product_a, product_b 및 product_c 열의 미지정 값을 나타내는 열인 quantity이다.

unpivot_for_clause는 product_code로, 측정값을 저장할 열이다.

unpivot_in_clause 절은 다음과 같다.

```sql
IN ( -- unpivot_in_clause
    product_a AS 'A',
    product_b AS 'B',
    product_c AS 'C'
)
```

이 명령은 Oracle이 product_a, product_b 및 product_c 열 값을 unpivot 하도록 지시한다.

다음 그림은 결과를 보여준다.

![unpivot-example](/assets/img/2021-05-13-oracle-unpivot/unpivot-example.png)

기본적으로 UNPIVOT 작업에서는 null-valued 행을 제외하므로 출력에 NULL이 표시되지 않는다.

다음 예에서는 UNPIVOT 절을 사용하여 product_a, product_b 및 product_c 열의 값을 행으로 전환하지만 null 값 행을 포함한다.

```sql
SELECT * FROM sale_stats
UNPIVOT INCLUDE NULLS(
    quantity
    FOR product_code 
    IN (
        product_a AS 'A',
        product_b AS 'B',
        product_c AS 'C'
    )
);
```

다음은 결과이다.

![unpivot-include-nulls-example](/assets/img/2021-05-13-oracle-unpivot/unpivot-include-nulls-example.png)

## 4. 여러 열 UNPIVOT
여러 열의 포장을 해제하는 예를 살펴본다.

* __첫째:__ sale_stats 테이블을 삭제하고 다시 만든다.

```sql
DROP TABLE sale_stats;

CREATE TABLE sale_stats (
    id INT PRIMARY KEY,
    fiscal_year INT,
    a_qty INT,
    a_value DEC(19,2),
    b_qty INT,
    b_value DEC(19,2)
);
```

* __둘째:__ sale_stats 테이블에 행을 삽입한다.

```sql
INSERT INTO sale_stats(id, fiscal_year, a_qty, a_value, b_qty, b_value)
VALUES(1, 2018, 100, 1000, 2000, 4000);

INSERT INTO sale_stats(id, fiscal_year, a_qty, a_value, b_qty, b_value)
VALUES(2, 2019, 150, 1500, 2500, 5000);
```

* __셋째:__ sale_stats 테이블에서 데이터를 조회한다.

```sql
SELECT * FROM sale_stats;
```

![unpivot-sample-table](/assets/img/2021-05-13-oracle-unpivot/unpivot-sample-table.png)

* __넷째:__ UNPIVOT 절을 사용하여 a_qty, a_value, b_qty 및 b_value 열의 값을 행으로 바꾼다.

```sql
SELECT * FROM sale_stats
UNPIVOT (
    (quantity, amount)
    FOR product_code
    IN (
        (a_qty, a_value) AS 'A',
        (b_qty, b_value) AS 'B'
    )
);
```

다음은 결과이다.

![unpivot-multiple-columns-example](/assets/img/2021-05-13-oracle-unpivot/unpivot-multiple-columns-example.png)

다음 그림은 전치 과정을 보여준다.

![unpivot-multiple-columns](/assets/img/2021-05-13-oracle-unpivot/unpivot-multiple-columns.png)

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-basics/oracle-unpivot/>
