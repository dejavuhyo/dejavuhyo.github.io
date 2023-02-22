---
title: Oracle MINUS
author: dejavuhyo
date: 2021-04-26 07:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-minus, minus, 오라클-minus, 오라클-차집합, 차집합]
---

## 1. Oracle MINUS
Oracle MINUS 연산자는 두 개의 쿼리를 비교하고 두 번째 쿼리에 의해 출력되지 않는 첫 번째 쿼리와 다른 행을 반환한다. 즉, MINUS 연산자는 한 결과 집합을 다른 결과 집합에서 뺀다.

다음은 Oracle MINUS 연산자의 구문이다.

```sql
SELECT
    column_list_1
FROM
    T1
MINUS 
SELECT
    column_list_2
FROM
    T2;
```

UNION 및 INTERSECT 연산자와 마찬가지로 다음 규칙을 준수해야 한다.

* 열의 수와 열의 순서가 일치해야 한다.

* 해당 열의 데이터 유형은 숫자 또는 문자와 같은 데이터 유형 그룹에 속해야 한다.

첫 번째 쿼리가 1, 2, 3을 포함하는 T1 결과 집합을 반환한다. 그리고 두 번째 쿼리는 2, 3, 4가 포함된 T2 결과 세트를 반환한다.

다음 그림은 T1 및 T2의 MINUS 결과를 보여준다.

![minus](/assets/img/2021-04-26-oracle-minus/minus.png)

## 2. MINUS examples
샘플 데이터베이스에서 contacts 및 employees 테이블을 참조한다.

![contacts-table](/assets/img/2021-04-26-oracle-minus/contacts-table.png)

![employees-table](/assets/img/2021-04-26-oracle-minus/employees-table.png)

다음 문은 오른쪽 쿼리에 없는 MINUS 연산자의 왼쪽 쿼리로부터 고유한 last_name을 반환한다.

```sql
SELECT
    last_name
FROM
    contacts
MINUS
SELECT
    last_name
FROM
    employees
ORDER BY
    last_name;
```

다음은 첫 번째 쿼리에서 반환되었지만 두 번째 쿼리의 결과 집합에서 찾을 수 없는 last_name이다.

![lastnames-example](/assets/img/2021-04-26-oracle-minus/lastnames-example.png)

다음 products 및 inventories 테이블을 참조한다.

![products-table](/assets/img/2021-04-26-oracle-minus/products-table.png)

![inventories-table](/assets/img/2021-04-26-oracle-minus/inventories-table.png)

다음 명령문은 products 테이블에서 제품 ID 목록을 반환하지만 inventories 테이블에 없다.

```sql
SELECT
    product_id
FROM
    products
MINUS
SELECT
    product_id
FROM
    inventories;
```

결과는 다음과 같다.

![minus-example](/assets/img/2021-04-26-oracle-minus/minus-example.png)

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-basics/oracle-minus/](https://www.oracletutorial.com/oracle-basics/oracle-minus/)
