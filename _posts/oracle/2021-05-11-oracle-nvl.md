---
title: Oracle NVL
author: dejavuhyo
date: 2021-05-11 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-nvl, nvl, 오라클-nvl, 오라클-null-치환]
---

## 1. Oracle NVL() 기능
Oracle NVL() 기능을 사용하면 쿼리 결과에서 null을 보다 의미 있는 대안으로 대체할 수 있다.

다음은 NVL() 함수의 구문을 보여준다.

```text
NVL(e1, e2)
```

NVL() 함수는 두 개의 인수를 받는다. e1이 null로 평가되면 NVL() 함수는 e2를 반환한다. e1이 null이 아닌 것으로 평가되면 NVL() 함수는 e1을 반환한다.

e1과 e2 인수는 같거나 다른 데이터 유형을 가질 수 있다. 데이터 유형이 서로 다르면 Oracle은 다음 규칙에 따라 데이터 유형을 다른 유형으로 변환한다.

* e1의 데이터 유형이 character인 경우, Oracle은 e2를 null과 비교하기 전에 e1의 데이터 유형으로 변환하고 e1의 문자 집합에서 VARCHAR2를 반환한다.

* e1의 데이터 유형이 numeric일 경우, Oracle은 숫자 우선 순위가 가장 높은 인수를 결정하고 다른 인수를 해당 데이터 유형으로 암시적으로 변환한 다음 해당 데이터 유형을 반환한다.

* Oracle은 한 데이터 유형을 다른 데이터 유형으로 암시적으로 변환할 수 없는 경우 오류가 발생한다.

## 2. NVL() function 예제
다음 예제에서는 첫 번째 인수가 null이 아니므로 100을 반환합니다.

```sql
SELECT
    NVL(100, 200)
FROM
    dual;
```

다음 예제에서는 첫 번째 인수가 null이므로 N/A를 반환한다.

```sql
SELECT
    NVL(NULL, 'N/A')
FROM
    dual;
```

샘플 데이터베이스에서 다음 주문 및 직원 표를 참조한다.

![orders-employees-tables](/assets/img/2021-05-11-oracle-nvl/orders-employees-tables.png)

다음 쿼리는 주문 ID와 각 판매 주문에 할당된 판매원을 반환한다.

```sql
SELECT
    order_id,
    NVL(first_name, 'Not Assigned')
FROM
    orders
LEFT JOIN employees
ON employee_id = salesman_id
WHERE
    EXTRACT(YEAR FROM order_date) = 2016
ORDER BY
    order_date;
```

![nvl-function-example](/assets/img/2021-05-11-oracle-nvl/nvl-function-example.png)

이 예에서는 2016년에 모든 판매 주문을 검색했다. 판매 주문의 salesal_id 열에 값이 없는 경우, LEFT JOIN 작업에 따라 first_name이 null이다.

NVL() 함수는 판매 주문에 할당된 판매원이 있으면 판매원의 이름을 반환하고, 그렇지 않으면 Not Assigned 문자열이 반환된다.

## 3. NVL() and CASE expression
NVL() 함수는 NULL에 대한 값을 테스트할 때 CASE 식과 유사하다.

```text
NVL (e1, e2)
```

다음과 같다.

```text
CASE
WHEN e1 IS NOT NULL THEN
    e1
ELSE
    e2
END
```

CASE 식을 사용하여 주문 ID와 세일즈맨을 반환하는 쿼리를 다음과 같이 다시 작성할 수 있다.

```sql
SELECT
    order_id,
    CASE
        WHEN first_name IS NOT NULL
        THEN first_name
        ELSE 'Not Assigned'
    END
FROM
    orders
LEFT JOIN employees
ON
    employee_id = salesman_id
WHERE
    EXTRACT(YEAR FROM order_date) = 2016
ORDER BY
    order_date;
```

## 4. NVL() vs. COALESCE()
COALESCE() 함수는 NVL() 함수의 일반화이다.

다음 함수는

```text
NVL(e1,e2)
```

아래와 동일한 결과를 반환한다.

```text
COALESCE (e1, e2)
```

그러나 COALESCE() 함수는 그 인수를 순서대로 평가하고 결과를 결정할 수 있을 때, 즉 첫 번째 non-NULL 인수를 찾을 수 있을 때 평가를 중단한다. 이 기능을 단락 평가라고 한다. 대조적으로, NVL() 함수는 결과를 결정하기 위해 모든 인수를 평가한다.

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-comparison-functions/oracle-nvl/](https://www.oracletutorial.com/oracle-comparison-functions/oracle-nvl/)
