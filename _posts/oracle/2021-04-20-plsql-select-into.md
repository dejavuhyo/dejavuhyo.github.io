---
title: PL/SQL SELECT INTO
author: dejavuhyo
date: 2021-04-20 06:30:00 +0900
categories: [Database, Oracle]
tags: [select-into, plsql-select-into]
---

## 1. SELECT INTO statement
SELECT INTO 문은 표의 단일 행을 변수로 가져오는 가장 간단하고 빠른 방법이다. 다음은 PL/SQL SELECT INTO 문의 구문을 보여준다.

```sql
SELECT
    select_list
INTO
    variable_list
FROM
    table_name
WHERE
    condition;
```

이 구문에서 variable_list의 열 수는 select_list의 변수 수(또는 레코드의 구성 요소 수)와 같아야 한다. 또한 해당 데이터 유형이 호환되어야 한다.

WHERE 절 외에도 SELECT 문에서 INNER JOIN, GROUP BY, HAVING 그리고 UNION과 같은 다른 절을 사용할 수 있다.

SELECT 문이 두 개 이상의 행을 반환하면 Oracle은 TOO_MANY_ROWS 예외를 발생시킨다. SELECT 문이 행을 반환하지 않으면 Oracle은 NO_DATA_FOUND 예외를 발생시킨다.

## 2. SELECT INTO examples
샘플 데이터베이스에 있는 고객과 연락처 테이블이다.

![customers-tables](/assets/img/2021-04-20-plsql-select-into/customers-tables.png)

### 1) 열 하나 선택 예제
다음 예에서는 SELECT INTO 문을 사용하여 고객 테이블의 기본 키인 고객 ID를 기반으로 고객의 이름을 가져온다.

```sql
DECLARE
    l_customer_name customers.name%TYPE;
BEGIN
    -- get name of the customer 100 and assign it to l_customer_name
    SELECT name INTO l_customer_name
    FROM customers
    WHERE customer_id = 100;
    -- show the customer name
    dbms_output.put_line( v_customer_name );
END;
```

이 예에서는 다음을 수행한다.

* **첫째:** 데이터 유형이 고객 테이블의 이름 열에 고정되는 l_customer_name 변수를 선언한다. 이 변수는 고객 이름을 유지한다.

* **둘째:** SELECT INTO 문을 사용하여 이름 열에서 값을 선택하고 l_customer_name 변수에 할당한다.

* **셋째:** dbms_output.put_line 절차에 따라 고객 이름을 보여준다.

고객 테이블에 고객 ID가 100인 행이 하나만 있기 때문에 코드 블록에 고객 이름이 표시된다.

```text
Verizon
```

이러한 행이 없으면 코드 블록은 처리되지 않은 NO_DATA_FOUND 예외와 함께 실패 한다.

### 2) 전체 행 선택 예제
다음 예에서는 특정 고객 ID에 대한 전체 행을 고객 테이블에서 가져온다.

```sql
DECLARE
    r_customer customers%ROWTYPE;
BEGIN
    -- get the information of the customer 100
    SELECT * INTO r_customer
    FROM customers
    WHERE customer_id = 100;
    -- show the customer info
    dbms_output.put_line( r_customer.name || ', website: ' || r_customer.website );
END;
```

결과는 다음과 같다.

```text
Verizon, website: http://www.verizon.com
```

이 예에서는 다음을 수행한다.

* **첫째:** customers 테이블의 행을 기준으로 레코드를 선언한다. 이 레코드는 customers 테이블의 전체 행을 유지한다.

* **둘째:** r_customer 레코드에 id가 100인 고객을 선택한다.

* **셋째:** 고객의 이름과 웹 사이트를 표시한다.

### 3) 다중 변수에 데이터 선택 예제
다음 예에서는 특정 고객 ID에 대한 고객 이름 및 연락처 테이블을 가져온다.

```sql
DECLARE
    l_customer_name customers.name%TYPE;
    l_contact_first_name contacts.first_name%TYPE;
    l_contact_last_name contacts.last_name%TYPE;
BEGIN
    -- get customer and contact names
    SELECT
        name,
        first_name,
        last_name
    INTO
        l_customer_name,
        l_contact_first_name,
        l_contact_last_name
    FROM
        customers
    INNER JOIN contacts USING( customer_id )
    WHERE
        customer_id = 100;
    -- show the information
    dbms_output.put_line( 
        l_customer_name || ', Contact Person: ' ||
        l_contact_first_name || ' ' || l_contact_last_name );
END;
```

Oracle은 다음과 같은 출력을 발행했다.

```text
Verizon, Contact Person: Elisha Lloyd
```

이 예에서는 다음을 수행한다.

* **첫째:** l_customer_name, l_contact_first_name, l_contact_last_name 변수를 선언하여 고객과 연락처의 이름을 유지한다.

* **둘째:** SELECT INTO 문을 사용하여 고객 ID 100의 고객 및 연락처 이름을 customers과 contacts 테이블에서 해당 변수 l_customer_name, l_contact_first_name, l_contact_last_name으로 가져온다.

* **셋째:** 고객 및 연락처 이름을 표시한다.

## 3. SELECT INTO common errors
SELECT 절의 열 및 식 수가 INTO 절의 변수 수보다 클 경우 Oracle은 다음 오류를 발생시킨다.

```text
ORA-00947: not enough values The INTO list contains fewer variables than the SELECT list.
```

선택 절의 열 및 식 수가 INTO 절의 변수 수보다 적을 경우 Oracle은 다음 오류를 발생시킨다.

```text
ORA-00913: too many values The INTO list contains more variables than the SELECT list.
```

선택 목록의 변수와 요소 수가 동일하지만 해당 데이터 유형이 호환되지 않는 경우 Oracle은 한 유형에서 다른 유형으로 암시적으로 변환할 수 없다. 그러면 다음 오류가 발생한다.

```text
ORA-06502: PL/SQL: numeric or value error
```

## [출처 및 참고]
* [https://www.oracletutorial.com/plsql-tutorial/plsql-select-into/](https://www.oracletutorial.com/plsql-tutorial/plsql-select-into/)
