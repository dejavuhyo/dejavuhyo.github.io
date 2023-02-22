---
title: PL/SQL CASE Statement
author: dejavuhyo
date: 2021-04-12 06:10:00 +0900
categories: [Database, Oracle]
tags: [case-statement, case, plsql-case]
---

## 1. Overview
CASE 문은 가능한 많은 시퀀스 중에서 실행할 문 순서를 하나 선택한다.

CASE 문에는 단순 CASE 문과 검색된 CASE 문 두 가지 유형이 있다. CASE 문의 두 유형 모두 선택적 ELSE 절을 지원한다.

## 2. Simple CASE statement
단순 CASE 문은 단일 식을 평가하고 결과를 일부 값과 비교한다.

단순 CASE 문에는 다음과 같은 구조가 있다.

```sql
CASE selector
WHEN selector_value_1 THEN
    statements_1
WHEN selector_value_1 THEN
    statement_2
...
ELSE
    else_statements
END CASE;
```

### 1) selector
selector는 한 번 평가하는 식이다.

셀렉터의 결과는 `selector_value_1` 및 `selector_value_2`와 같은 몇 가지 대안 중 하나를 선택하는 데 사용된다.

### 2) WHEN selector_value THEN statements
selector 값, 즉 `selector_value_1`, `selector_value_2` 등을 순차적으로 평가한다. selector 값의 결과가 selector 결과와 같다면, 관련된 문장 시퀀스가 실행되고 CASE 문이 종료된다. 또한 후속 selector 값은 평가되지 않는다.

### 3) ELSE else_statements
WHERE 절의 값이 CASE 절의 selector의 결과와 일치하지 않는 경우, ELSE 절의 문장 시퀀스가 실행된다.
ELSE 절은 선택적이므로 생략할 수 있다. 그러나 PL/SQL은 다음을 암시적으로 사용한다.

```text
ELSE
    RAISE CASE_NOT_FOUND;
```

즉, 사용자가 ELSE 절을 지정하지 않고 CASE 식의 결과가 WHEN 절의 값과 일치하지 않으면 PL/SQL은 `CASE_NOT_FOUND` 오류를 발생시킨다.

CASE 문의 동작은 IF THEN 문과 다르다. IF THEN 문에 ELSE 절이 없고 조건이 충족되지 않으면 PL/SQL은 오류를 발생시키는 대신 아무 작업도 하지 않는다.

## 3. Simple CASE statement example
단일 값(c_grade)을 가능한 많은 값 'A', 'B', 'C', 'D' 및 'F'와 비교한다.

```sql
DECLARE
    c_grade CHAR( 1 );
    c_rank  VARCHAR2( 20 );
BEGIN
    c_grade := 'B';
    CASE c_grade
    WHEN 'A' THEN
        c_rank := 'Excellent';
    WHEN 'B' THEN
        c_rank := 'Very Good';
    WHEN 'C' THEN
        c_rank := 'Good';
    WHEN 'D' THEN
        c_rank := 'Fair';
    WHEN 'F' THEN
        c_rank := 'Poor';
    ELSE
        c_rank := 'No such grade';
    END CASE;
DBMS_OUTPUT.PUT_LINE( c_rank );
END;
```

## 4. Searched CASE statement
검색된 CASE 문은 여러 Boolean 식을 평가하고 TRUE로 평가하는 첫 번째 조건과 관련된 문의 순서를 실행한다.

검색된 CASE 문의 구조는 다음과 같다.

```sql
CASE
WHEN condition_1 THEN statements_1
WHEN condition_2 THEN statements_2
...
WHEN condition_n THEN statements_n
[ ELSE
    else_statements ]
END CASE;]
```

검색된 CASE 문은 아래 규칙을 따른다.

* 'WHEN' 절의 조건을 위에서 아래로 순서대로 평가한다.

* 조건이 TRUE로 평가되는 WHEN 절과 관련된 문의 시퀀스가 실행된다. 둘 이상의 조건이 TRUE로 평가되면 첫 번째 조건만 실행된다.

* 조건이 TRUE로 평가되지 않으면 ELSE 절의 else_statements가 실행된다. ELSE 절을 건너뛰고 식이 TRUE가 아닌 경우 CASE_NOT_FOUND 예외가 발생한다.

## 5. Searched CASE statement example
검색된 CASE 문을 사용하여 판매 수익을 기준으로 판매 수수료를 계산하는 방법을 보여준다.

```sql
DECLARE
    n_sales NUMBER;
    n_commission NUMBER;
BEGIN
    n_sales := 150000;
    CASE
    WHEN n_sales > 200000 THEN
        n_commission := 0.2;
    WHEN n_sales >= 100000 AND n_sales < 200000 THEN
        n_commission := 0.15;
    WHEN n_sales >= 50000 AND n_sales < 100000 THEN
        n_commission := 0.1;
    WHEN n_sales > 30000 THEN
        n_commission := 0.05;
    ELSE
        n_commission := 0;
    END CASE;

    DBMS_OUTPUT.PUT_LINE( 'Commission is ' || n_commission * 100 || '%' );
END;
```

판매 수익이 15만 개로 설정되었다. FALSE로 계산된 첫 번째 식이다.

```text
n_sales > 200000
```

그러나 두 번째 표현은 TRUE로 평가되고 판매 수수료는 15%로 설정되었다.

```text
n_commission := 0.15;
```

PL/SQL은 TRUE로 평가하는 첫 번째 조건을 찾으면 후속 조건 평가를 중지한다. 따라서 예제에서는 PL/SQL이 CASE 문의 마지막 두 조건을 평가하지 않는다. ELSE 문 절도 실행되지 않는다.

## 6. Simple CASE or searched CASE statement
여러 Boolean 식의 결과를 기반으로 일련의 문을 실행하려면 검색한 CASE 문을 사용하고 단일 식의 결과를 기반으로 일련의 문을 실행하려면 간단한 CASE 문을 사용한다.

## 7. PL/SQL CASE statement vs CASE expression
PL/SQL에는 CASE 문과 유사한 CASE 식도 있다.

CASE 식은 조건 목록을 평가하고 가능한 여러 결과 식 중 하나를 반환한다.

CASE 식의 결과는 단일 값인 반면 CASE 문의 결과는 일련의 문들의 실행이다.

## [출처 및 참고]
* [https://www.oracletutorial.com/plsql-tutorial/plsql-case-statement/](https://www.oracletutorial.com/plsql-tutorial/plsql-case-statement/)
