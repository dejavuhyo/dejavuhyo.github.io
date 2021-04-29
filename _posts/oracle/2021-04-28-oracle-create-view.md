---
title: Oracle CREATE VIEW
author: dejavuhyo
date: 2021-04-28 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-create-view, create-view, 오라클-create-view, 오라클-뷰-생성, 뷰-생성]
---

## 1. CREATE VIEW syntax
데이터베이스에 새로운 view를 생성하려면 CREATE VIEW 문을 사용한다.

```sql
CREATE [OR REPLACE] VIEW view_name [(column_aliases)] AS
    defining-query
[WITH READ ONLY]
[WITH CHECK OPTION]
```

### 1) OR REPLACE
OR REPLACE 옵션은 기존 view의 정의를 대체한다. view에 대한 다양한 권한을 부여한 경우 유용하다. DROP VIEW 및 CREATE VIEW를 사용하여 view의 정의를 변경하면, Oracle에서 당신이 원하지 않는 권한이 제거되기 때문이다. 이 문제를 방지하려면 view 권한을 유지하는 CREATE 또는 REPLACE 절을 사용할 수 있다.

### 2) FORCE
일반적으로 기존 테이블을 기준으로 새로운 view를 만든다.

그러나 나중에 생성할 테이블이나 view 생성 시 액세스할 수 있는 권한이 없는 테이블을 기준으로 view를 생성할 수도 있다. 이 경우 FORCE 옵션을 사용할 수 있다.

### 3) column-aliases
일반적으로 정의 쿼리의 선택 목록에서 파생된 보기의 열 이름이다. 그러나 정의 쿼리의 열 이름에는 보기 정의에 사용할 수 없는 함수 또는 식이 포함될 수 있다.

이 문제를 해결하려면 두 가지 옵션이 있다.

* 정의 쿼리의 SELECT 절에 있는 명명 규칙을 준수하는 열 별칭을 사용한다.

* CREATE VIEW 절과 AS 절 사이의 view 열에 대한 column aliases를 명시적으로 지정한다.

### 4) AS defining-query
정의 쿼리는 보기의 열과 행을 정의하는 SELECT 문이다.

### 5) WITH READ ONLY
WITH READ ONLY 절은 기본 테이블이 보기를 통해 변경되지 않도록 한다.

### 6) WITH CHECK OPTION
WITH CHECK OPTION 절은 정의 쿼리에 포함되지 않은 행을 생성하는 기본 테이블의 변경으로부터 view를 보호한다.

## 2. CREATE VIEW examples
샘플 데이터베이스의 테이블을 기반으로 새로운 view를 생성하는 몇 가지 예를 살펴본다.

### 1) Creating a view example
샘플 데이터베이스에서 다음 직원 표를 참조한다.

![employees-table](/assets/img/2021-04-28-oracle-create-view/employees-table.png)

다음 명령문은 직원 테이블을 기준으로 semployee_yos라는 이름의 보기를 생성한다. view에는 직원 ID, 이름 및 근속 연수가 표시된다.

```sql
CREATE VIEW employee_yos AS
SELECT
    employee_id,
    first_name || ' ' || last_name full_name,
    FLOOR( months_between( CURRENT_DATE, hire_date )/ 12 ) yos
FROM
    employees;
```

full_name은 ```first_name || ' |' || last_name```그리고 yos는 ```FLOOR( months_between( CURRENT_DATE, hire_date )/ 12 )```를 사용하기 때문에 view에 대한 column 이름을 정의하지 않았다.

쿼리에 column aliases를 사용하지 않으려면 CREATE VIEW 절에서 해당 별칭을 정의해야 한다.

```sql
CREATE VIEW employee_yos (employee_id, full_name, yos) AS
SELECT
    employee_id,
    first_name || ' ' || last_name,
    FLOOR( months_between( CURRENT_DATE, hire_date )/ 12 )
FROM
    employees;
```

다음 쿼리는 근속 연수가 15년인 직원을 반환한다.

```sql
SELECT
    *
FROM
    employee_yos
WHERE
    yos = 15
ORDER BY
    full_name;
```

![create-view-example](/assets/img/2021-04-28-oracle-create-view/create-view-example.png)

### 2) Creating a read-only view example
다음 고객 표를 참조한다.

![customers-table](/assets/img/2021-04-28-oracle-create-view/customers-table.png)

customer_credits라는 이름의 읽기 전용 보기를 생성하는데, 이 보기는 customers 테이블을 기반으로 한다. 보기는 고객 ID, 고객 이름 및 신용 한도라는 세 가지 열을 포함한다.

```sql
CREATE OR REPLACE VIEW customer_credits(
    customer_id,
    name,
    credit
) AS
SELECT
    customer_id,
    name,
    credit_limit
FROM
    customers
WITH READ ONLY;
```

### 3) Creating a Join view example
조인 view는 정의 쿼리에 조인(예: inner join 또는 left join)이 포함된 view이다. 다음 명령문은 정의 쿼리에 orders, order_items 및 products의 세 테이블을 조인하는 조인 절이 포함된 backlogs라는 view를 생성한다.

![orders-order-items-products-tables](/assets/img/2021-04-28-oracle-create-view/orders-order-items-products-tables.png)

```sql
CREATE OR REPLACE VIEW backlogs AS
    SELECT
    product_name,
    EXTRACT(
        YEAR
    FROM
        order_date
    ) YEAR,
    SUM( quantity * unit_price ) amount
FROM
    orders
INNER JOIN order_items
    USING(order_id)
INNER JOIN products
    USING(product_id)
WHERE
    status = 'Pending'
GROUP BY
    EXTRACT(
        YEAR
    FROM
        order_date
    ),
    product_name;
```

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-view/oracle-create-view/>
