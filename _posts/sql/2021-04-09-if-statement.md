---
title: IF Statement
author: dejavuhyo
date: 2021-04-09 06:00:00 +0900
categories: [Database, SQL]
tags: [if-statement, if, sql-if]
---

## 1. Overview
IF 문을 사용하면 조건에 따라 일련의 문을 실행하거나 건너뛸 수 있다. IF 문에는 세 가지 형식이 있다.

* IF THEN

* IF THEN ELSE

* IF THEN ELSIF

## 2. IF THEN statement
다음은 IF THEN 문의 구조를 보여준다.

```sql
IF condition THEN
    statements;
END IF;
```

조건은 항상 TRUE, FALSE 또는 NULL로 평가되는 Boolean 식이다.

조건이 TRUE로 평가될 경우, THEN 실행 후 문장이 표시된다. 그렇지 않으면 IF 문이 아무런 작업도 수행하지 않는다.

## 2. IF THEN statement example
판매 수익이 100,000보다 크기 때문에 THEN과 END IF 사이의 문이 실행된다.

```sql
DECLARE n_sales NUMBER := 2000000;
BEGIN
    IF n_sales > 100000 THEN
        DBMS_OUTPUT.PUT_LINE( 'Sales revenue is greater than 100K ' );
    END IF;
END;
```

### 1) Tip 1: Avoid clumsy IF statement

```sql
DECLARE
    b_profitable BOOLEAN;
    n_sales NUMBER;
    n_costs NUMBER;
BEGIN
    b_profitable := false;
    IF n_sales > n_costs THEN
        b_profitable := true;
    END IF;
END;
```

IF 문장은 판매 수익이 원가보다 높은지 여부를 결정하고 그에 따라 b_profitable 변수를 업데이트한다.

Boolean 식의 결과를 다음과 같이 Boolean 변수에 직접 할당할 수 있기 때문에 이 IF 문을 Boolean IF 문이라고 한다.

```text
b_profitable := n_sales > n_costs;
```

### 2) Tip 2: Avoid evaluating Boolean variables
부울 변수는 항상 TRUE, FALSE 또는 NULL이다. 따라서 다음 비교는 불필요하다.

```sql
IF b_profitable = TRUE THEN
    DBMS_OUTPUT.PUT_LINE( 'This sales deal is profitable' );
END IF;
```

대신 다음을 사용한다.

```sql
IF b_profitable THEN
    DBMS_OUTPUT.PUT_LINE( 'This sales deal is profitable' );
END IF;
```

## 3. IF THEN ELSE statement
IF THEN HERSE 문에는 다음과 같은 구조가 있다.

```sql
IF condition THEN
    statements;
ELSE
    else_statements;
END IF;
```

조건이 TRUE로 평가되면 THEN과 ELSE 사이의 문이 실행된다. 조건이 FALSE 또는 NULL로 평가되는 경우 ELSE와 END IF 사이의 else_statements를 실행한다.

## 4. IF THEN ELSE statement example
판매 수익이 200,000보다 클 경우 판매 수수료를 10%로 설정한다. 그렇지 않으면 판매 수수료가 5%로 설정된다.

```sql
DECLARE
    n_sales NUMBER := 300000;
    n_commission NUMBER( 10, 2 ) := 0;
BEGIN
    IF n_sales > 200000 THEN
        n_commission := n_sales * 0.1;
    ELSE
        n_commission := n_sales * 0.05;
    END IF;
END;
```

## 5. IF THEN ELSIF statement
다음은 IF THENELSIF 문의 구조를 보여준다.

```sql
IF condition_1 THEN
    statements_1
ELSIF condition_2 THEN
    statements_2
[ ELSIF condition_3 THEN
    statements_3
]
...
[ ELSE
    else_statements
]
END IF;
```

이 구조에서는 첫 번째 조건인 IF와 THEN 사이의 조건이 항상 평가된다. ELSIF와 THEN 사이의 서로 다른 조건은 앞의 조건이 FALSE인 경우에만 평가된다. 예를 들어 condition_2는 condition_1이 false인 경우에만 평가되고 condition_3은 condition_2가 false인 경우에만 평가된다.

조건이 참이면 다른 후속 조건은 평가되지 않는다. true가 없는 경우 ELSE와 ENDIF 사이의 else_statements가 실행된다. ELSE 절을 건너뛰고 조건이 참이 아닌 경우 IF THEN ELSIF는 아무 작업도 수행하지 않는다.

## 6. IF THEN ELSIF statement example
IF THEN ELSIF 문을 사용하여 판매 수익을 기준으로 판매 수수료를 설정한다.

```sql
DECLARE
    n_sales NUMBER := 300000;
    n_commission NUMBER( 10, 2 ) := 0;
BEGIN
    IF n_sales > 200000 THEN
        n_commission := n_sales * 0.1;
    ELSIF n_sales <= 200000 AND n_sales > 100000 THEN
        n_commission := n_sales * 0.05;
    ELSIF n_sales <= 100000 AND n_sales > 50000 THEN
        n_commission := n_sales * 0.03;
    ELSE
        n_commission := n_sales * 0.02;
    END IF;
END;
```

## 7. Nested IF statement
IF 문을 다른 IF 문에 중첩시킬 수 있다.

```sql
IF condition_1 THEN
    IF condition_2 THEN
        nested_if_statements;
    END IF;
ELSE
    else_statements;
END IF;
```

그러나 중첩 수준이 너무 많으면 코드를 읽고 유지 관리하기가 어렵기 때문에 IF 문을 중첩하지 않아야 합니다.

## [출처 및 참고]
* <https://www.oracletutorial.com/plsql-tutorial/plsql-if/>
