---
title: Oracle ROLLUP
author: dejavuhyo
date: 2021-04-27 07:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-rollup, rollup, 오라클-rollup, 오라클-합계, 합계]
---

## 1. Oracle ROLLUP
샘플 데이터베이스에서 orders 및 order_items 테이블을 참고한다.

![orders-order-items-tables](/assets/img/2021-04-27-oracle-rollup/orders-order-items-tables.png)

다음 쿼리는 2017년 고객의 판매 수익을 반환한다. Shipped 상태의 판매 주문 순가치를 계산하고 판매원을 담당합니다.

```sql
SELECT
    customer_id,
    SUM(quantity * unit_price) amount
FROM
    orders
INNER JOIN order_items USING (order_id)
WHERE
    status = 'Shipped'
    AND salesman_id IS NOT NULL
    AND EXTRACT(YEAR FROM order_date) = 2017
GROUP BY
    customer_id
ORDER BY
    amount DESC;
```

![group-by-clause](/assets/img/2021-04-27-oracle-rollup/group-by-clause.png)

금액 열의 값 합계를 얻으려면 다음 하위 쿼리를 사용할 수 있다.

```sql
SELECT
    SUM(amount)
FROM
    (
        SELECT
            customer_id,
            SUM(quantity * unit_price) amount
        FROM
            orders
        INNER JOIN order_items USING (order_id)
        WHERE
            status = 'Shipped'
            AND salesman_id IS NOT NULL
            AND EXTRACT(YEAR FROM order_date) = 2017
        GROUP BY
            customer_id
    );
```

![using-subquery](/assets/img/2021-04-27-oracle-rollup/using-subquery.png)

Oracle은 ROLLUP을 사용하여 총합계를 보다 빠르고 효율적으로 계산할 수 있는 방법을 제공한다.

```sql
SELECT
    customer_id,
    SUM(quantity * unit_price) amount
FROM
    orders
INNER JOIN order_items USING (order_id)
WHERE
    status = 'Shipped'
    AND salesman_id IS NOT NULL
    AND EXTRACT(YEAR FROM order_date) = 2017
GROUP BY
    ROLLUP(customer_id);
```

![rollup-example](/assets/img/2021-04-27-oracle-rollup/rollup-example.png)

이 쿼리에서는 ROLLUP 식을 사용하여 선택한 주문의 총 매출액을 계산했다.

출력에서 명확히 알 수 있듯이 customer_id 열에 NULL 값이 있는 행은 전체 행을 나타낸다. 전체 총 행의 금액 열은 출력에 있는 모든 금액의 합계를 보여 준다.

## 2. ROLLUP syntax
ROLLUP은 GROUP BY 절의 확장이다. 롤업은 전체 총합과 함께 열(또는 기준) 그룹에 걸쳐 여러 수준의 하위 레벨을 계산한다.

다음은 ROLLUP의 구문을 보여준다.

```sql
SELECT
    col1,
    col2,
    aggregate(col3)
FROM
    table_name
GROUP BY
    ROLLUP (col1, col2);
```

위의 쿼리 구문에서는 ROLLUP에 지정된 그룹화 열에 따라 가장 자세한 수준에서 총합계로 롤업하는 부분합을 만든다.

ROLLUP은 다음과 같이 작동한다.

* **첫째:** GROUP BY 절에서 표준 집계 값을 계산한다.

* **둘째:** 오른쪽에서 왼쪽으로 col2 및 col2 열인 그룹화 열의 상위 수준 부분합을 점진적으로 만든다.

* **셋째:** 총합계를 계산한다.

ROLLUP절은 총합계를 더한 값에 지정된 그룹화 열 수와 동일한 그룹화 집합의 수를 생성한다. 즉, ROLLUP에 n 열이 나열된 경우, ROLLUP과 함께 n+1 수준의 부분합을 얻을 수 있다.

위의 구문에서 ROLLUP 절은 다음과 같은 그룹화 집합을 생성한다.

* (col1, col2)

* (col2)

* (grand total)

출력의 행 수는 그룹화 열의 고유한 값 조합 수에서 파생된다.

부분합의 수를 줄이기 위해 다음 구문에 표시된 부분 ROLLUP을 수행할 수 있다.

```sql
SELECT
    col1,
    col2,
    aggregate(col3)
FROM
    table_name
GROUP BY
    col1,
    ROLLUP(col2);
```

## 3. More ROLLUP examples

다음 예에서는 ROLLUP을 사용하여 세일즈맨 및 고객별 판매 값을 반환한다.

```sql
SELECT
    salesman_id,
    customer_id,
    SUM(quantity * unit_price) amount
FROM
    orders
INNER JOIN order_items USING (order_id)
WHERE
    status = 'Shipped'
    AND salesman_id IS NOT NULL
    AND EXTRACT(YEAR FROM order_date) = 2017
GROUP BY
    ROLLUP(salesman_id,customer_id);
```

![rollup-multiple-columns-example](/assets/img/2021-04-27-oracle-rollup/rollup-multiple-columns-example.png)

출력에서 볼 수 있듯이 쿼리는 다음 행 집합을 반환했다.

* ROLLUP 식을 사용하지 않고 GROUP BY 절에 의해 반환되는 정규 집계 행이다.

* 세일즈맨과 고객의 각 조합에 대해 세일즈맨 전체에서 집계하는 첫 번째 수준의 부분합이다.

* 두 번째 수준의 하위 총계는 각 세일즈맨에 대해 세일즈맨과 고객에 걸쳐 집계된다.

* 총합계 행이다.

다음 쿼리는 부분 ROLLUP을 수행한다.

```sql
SELECT
    salesman_id,
    customer_id,
    SUM(quantity * unit_price) amount
FROM
    orders
INNER JOIN order_items USING (order_id)
WHERE
    status = 'Shipped'
    AND salesman_id IS NOT NULL
    AND EXTRACT(YEAR FROM order_date) = 2017
GROUP BY
    salesman_id,
    ROLLUP(customer_id);
```

![partial-roll-up](/assets/img/2021-04-27-oracle-rollup/partial-roll-up.png)

쿼리는 판매원과 고객의 각 조합에 대해 판매원 전체 집계에 GROUP BY 되는 ROLLUP 및 부분합을 사용하지 않고 GROUP BY 절에 의해 반환되는 정규 집계 행을 출력한다.

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-basics/oracle-rollup/](https://www.oracletutorial.com/oracle-basics/oracle-rollup/)
