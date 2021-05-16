---
title: PL/SQL Function
author: dejavuhyo
date: 2021-05-17 06:00:00 +0900
categories: [Database, Oracle]
tags: [function, plsql-function, 함수, plsql-함수]
---

## 1. PL/SQL 함수 생성
procedure와 유사하게, PL/SQL 함수는 오라클 데이터베이스에 스키마 개체로 저장된 재사용 가능한 프로그램 단위이다. 다음은 함수를 만드는 구문을 보여 준다.

```sql
CREATE [OR REPLACE] FUNCTION function_name (parameter_list)
    RETURN return_type
IS
[declarative section]

BEGIN

[executable section]

[EXCEPTION]

[exception-handling section]

END;
```

함수는 헤더와 바디로 구성된다.

함수 헤더에는 반환된 값의 데이터 형식을 지정하는 RETURN 절과 함수 이름이 있다. 기능의 각 파라미터는 IN, OUT 또는 INOUT 모드일 수 있다.

기능 바디는 선언적 섹션, 실행 가능한 섹션 및 예외 처리 섹션의 세 섹션으로 구성된 절차 본체와 동일하다.

* 선언 섹션은 IS와 BEGIN 키워드 사이에 있다. 여기서 변수, 상수, 커서 및 사용자 정의 유형을 선언할 수 있다.

* 실행 가능한 섹션은 BEGIN과 END 키워드 사이에 있다. 여기서 실행 가능한 문을 배치할 수 있다. procedure와 달리 실행 가능한 문에 RETURN 문이 하나 이상 있어야 한다.

* 예외 처리 섹션은 예외 처리기 코드를 넣는 곳이다.

이 세 절에서는 실행 가능한 섹션만 필요하며 나머지 섹션은 선택 사항이다.

## 2. 함수 예제
다음 예제에서는 연도별 총 매출을 계산하는 함수를 만든다.

```sql
CREATE OR REPLACE FUNCTION get_total_sales (
    in_year PLS_INTEGER
)
RETURN NUMBER
IS
    l_total_sales NUMBER := 0;
BEGIN
    -- get total sales
    SELECT SUM(unit_price * quantity)
    INTO l_total_sales
    FROM order_items
    INNER JOIN orders USING (order_id)
    WHERE status = 'Shipped'
    GROUP BY EXTRACT(YEAR FROM order_date)
    HAVING EXTRACT(YEAR FROM order_date) = in_year;

    -- return the total sales
    RETURN l_total_sales;
END;
```

Oracle SQL Developer에서 기능을 컴파일하려면 아래 그림과 같이 Run Statement 버튼을 클릭한다.

![function-compile](/assets/img/2021-05-17-plsql-function/function-compile.png)

함수가 컴파일되면 기능 노드에서 찾을 수 있다.

![function-list](/assets/img/2021-05-17-plsql-function/function-list.png)

## 3. 함수 호출
동일한 유형의 식을 사용하는 모든 위치에서 함수를 사용한다. 다음과 같은 다양한 위치에서 함수를 호출할 수 있다.

### 1) 할당 문

```sql
DECLARE
    l_sales_2017 NUMBER := 0;
BEGIN
    l_sales_2017 := get_total_sales (2017);
    DBMS_OUTPUT.PUT_LINE('Sales 2017: ' || l_sales_2017);
END;
```

### 2) 부울 표현식

```sql
BEGIN
    IF get_total_sales (2017) > 10000000 THEN
        DBMS_OUTPUT.PUT_LINE('Sales 2017 is above target');
    END IF;
END;
```

### 3) SQL 문

```sql
SELECT
    get_total_sales(2017)
FROM
    dual;
```

## 4. 함수 편집
기존 함수를 편집하고 다시 컴파일하려면 다음 단계를 수행한다.

* __첫째:__ 편집할 함수 이름을 클릭한다.

* __둘째:__ 코드를 편집한다.

* __셋째:__ 컴파일 메뉴 옵션을 클릭하여 코드를 다시 컴파일한다.

![function-edit-and-recompile](/assets/img/2021-05-17-plsql-function/function-edit-and-recompile.png)

## 5. 함수 제거
DROP FUNCTION은 오라클 데이터베이스에서 함수를 삭제한다. 함수를 제거하기 위한 구문은 간단하다.

```sql
DROP FUNCTION function_name;
```

DROP FUNCTION 키워드는 삭제할 함수 이름이다.

예를 들어, 다음 명령문은 GET_TOTAL_SALES 함수를 삭제한다.

```sql
DROP FUNCTION get_total_sales;
```

Oracle은 GET_TOTAL_SALES 기능이 삭제되었음을 나타내는 메시지를 발행했다.

```text
Function GET_TOTAL_SALES dropped.
```

SQL Developer를 사용하여 함수를 삭제하려는 경우 다음 단계를 사용할 수 있다.

* __첫째:__ 삭제할 함수 이름을 마우스 오른쪽 버튼으로 누른다.

* __둘째:__ Drop… 메뉴 옵션을 선택한다.

* __셋째:__ Apply 버튼을 클릭하여 삭제를 확인한다.

![function-drop](/assets/img/2021-05-17-plsql-function/function-drop.png)

![function-drop-confirmation](/assets/img/2021-05-17-plsql-function/function-drop-confirmation.png)

## [출처 및 참고]
* <https://www.oracletutorial.com/plsql-tutorial/plsql-function/>
