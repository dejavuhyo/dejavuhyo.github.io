---
title: Oracle INSERT INTO SELECT
author: dejavuhyo
date: 2021-04-07 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-insert-into-select, insert-into-select, 오라클-insert-into-select]
---

## 1. Overview
테이블에서 데이터를 선택하여 다른 테이블에 삽입하려는 경우가 있다. 이 작업을 수행하려면 Oracle INSERT INTO SELECT 문을 사용한다.

* SQL

```sql
INSERT INTO target_table (col1, col2, col3)
SELECT
    col1,
    col2,
    col3
FROM
    source_table
WHERE
    condition
```

Oracle INSERT INTO SELECT 문을 사용하려면 원본 테이블과 대상 테이블의 데이터 유형이 일치해야 한다.

원본 테이블에서 대상 테이블로 모든 행을 복사하려면 WHERE 절을 제거한다. 그렇지 않으면 소스 테이블에서 대상 테이블로 복사해야 하는 행을 지정할 수 있다.

## 2. Examples

### 1) Insert all sales data example

* Create SQL

```sql
CREATE TABLE sales (
    customer_id NUMBER,
    product_id NUMBER,
    order_date DATE NOT NULL,
    total NUMBER(9,2) DEFAULT 0 NOT NULL,
    PRIMARY KEY(customer_id, product_id, order_date)
)
```

orders 및 order_items table의 판매 요약을 sales 테이블에 삽입한다.

* SQL

```sql
INSERT INTO sales(customer_id, product_id, order_date, total)
SELECT
    customer_id,
    product_id,
    order_date,
    SUM(quantity * unit_price) amount
FROM
    orders
INNER JOIN
    order_items USING(order_id)
WHERE
    status = 'Shipped'
GROUP BY
    customer_id,
    product_id,
    order_date
```

삽입을 확인하기 위해 sales 테이블에서 데이터를 검색한다.

* SQL

```sql
SELECT
    *
FROM
    sales
ORDER BY
    order_date DESC,
    total DESC
```

* 결과

![statement-example](/assets/img/2021-04-07-oracle-insert-into-select/statement-example.png)

### 2) Insert partial sales data example
2017년 판매 요약 데이터만 새 테이블로 복사하려고 한다. 이렇게 하려면 먼저 sales_2017이라는 새 테이블을 다음과 같이 만든다.

* Create SQL

```sql
CREATE TABLE sales_2017 AS
SELECT
    *
FROM
    sales
WHERE
    1 = 0
```

WHERE 절의 조건은 판매 테이블의 데이터가 sales_2017 테이블에 복사되지 않도록 보장한다.

WHERE 절과 함께 Oracle INSERT INT SELECT를 사용하여 2017년 세일즈 데이터를 sales_2017 테이블에 복사한다.

* SQL

```sql
INSERT INTO  sales_2017
SELECT
    customer_id,
    product_id,
    order_date,
    SUM(quantity * unit_price) amount
FROM
    orders
INNER JOIN
    order_items USING(order_id)
WHERE
    status = 'Shipped'
AND
    EXTRACT(year from order_date) = 2017
GROUP BY
    customer_id,
    product_id,
    order_date
```

SELECT 문 결과가 sales_2017 테이블의 열에 해당하는 값을 가지기 때문에 INSERT INTO 절에 열 목록을 지정하지 않았다.

또한 SELECT 문장의 WHERE 조항에 2017년 매출 데이터만 검색하도록 조건을 추가하였다.

* 결과

![copy-data-partially](/assets/img/2021-04-07-oracle-insert-into-select/copy-data-partially.png)

### 3) Insert partial data and literal value example
새 제품을 발표하기 위해 모든 고객에게 전자 메일을 보낸다고 가정한다. 이를 위해 고객 데이터를 별도의 테이블에 복사하고 전자 메일 전송 상태를 추적할 수 있다.

먼저 customer_lists라는 새 테이블을 만든다.

* Create SQL

```sql
CREATE TABLE customer_lists(
    list_id NUMBER GENERATED BY DEFAULT AS IDENTITY,
    first_name varchar2(255) NOT NULL,
    last_name varchar2(255) NOT NULL,
    email varchar2(255) NOT NULL,
    sent NUMBER(1) NOT NULL,
    sent_date DATE,
    PRIMARY KEY(list_id)
)
```

연락처 테이블에서 customer_lists 테이블로 데이터를 복사한다.

* SQL

```sql
INSERT INTO
customer_lists(
    first_name,
    last_name,
    email,
    sent
)
SELECT
    first_name,
    last_name,
    email,
    0
FROM
    contacts
```

연락처 테이블에서 데이터를 검색하는 것 외에도 리터럴 0을 보낸 열의 값으로 사용했다.

* 결과

![with-literals-example](/assets/img/2021-04-07-oracle-insert-into-select/with-literals-example.png)

이 예는 데모를 위한 것이며, 보낸 열의 정의에 DEFAULT 0을 추가할 수 있다.

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-basics/oracle-insert-into-select/](https://www.oracletutorial.com/oracle-basics/oracle-insert-into-select/)
