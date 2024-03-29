---
title: Oracle COALESCE
author: dejavuhyo
date: 2021-05-10 07:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-coalesce, coalesce, 오라클-coalesce]
---

## 1. Oracle COALESCE() 기능
Oracle COALESCE() 함수는 인수 목록을 받아 null이 아닌 값으로 평가되는 첫 번째 인수를 반환한다.

다음은 Oracle COALESCE() 기능의 구문을 보여 준다.

```text
COALESCE(e1, e2, ..., en)
```

이 구문에서 COALESCE() 함수는 목록에서 Null이 아닌 첫 번째 식을 반환한다. 두 개 이상의 표현식이 필요하다. 모든 표현식이 null로 평가되는 경우 함수는 null을 반환한다.

다음 예제는 null이 아닌 첫 번째 인수이기 때문에 하나를 반환한다.

```sql
SELECT
    COALESCE(NULL,1) -- return 1
FROM
    dual;
```

```text
   RESULT
---------
        1
```

다음 예제는 모든 인수가 null이므로 null을 반환한다.

```sql
SELECT
    COALESCE(NULL,NULL,NULL) -- return NULL
FROM
    dual;
```

### 1) Return type
모든 인수의 데이터 유형이 동일한 경우 COALESCE() 함수는 해당 데이터 유형의 값을 반환한다.

인수가 서로 다른 데이터 유형을 갖는 경우 COALESCE() 함수는 모든 인수를 첫 번째 Null이 아닌 인수의 데이터 형식으로 암시적으로 변환한다. 변환에 실패할 경우 Oracle에서 오류를 발생한다.

다음 예제에서는 모든 인수가 문자이므로 문자 유형의 값을 반환한다.

```sql
SELECT
    COALESCE(NULL,'A','B') result
FROM
    dual;
```

```text
 RESULT
-------
      A
```

마찬가지로 다음 예제에서는 숫자 유형의 값을 반환한다.

```sql
SELECT
    COALESCE(NULL,1,2) result
FROM
    dual;
```

```text
   RESULT
---------
        1
```

그러나 다음 예제에서는 COALESCE() 함수에 대해 서로 다른 유형의 인수를 사용한다.

```sql
SELECT
    COALESCE(NULL,1,'A')
FROM
    dual;
```

Oracle에서 다음 오류를 발생시켰다.

```text
ORA-00932: inconsistent datatypes: expected NUMBER got CHAR
```

COALESCE() 함수가 세 번째 인수의 문자 유형을 오류를 초래한 두 번째 인수의 숫자 유형으로 변환하려고 했기 때문이다.

### 2) Short-circuit evaluation
COALESCE() 기능은 단락 평가를 사용한다. 이는 함수가 첫 번째 표현식이 null이 아닌 값으로 평가되면 나머지 식에 대한 평가를 중단한다는 의미이다.

다음 예제를 고려한다.

```sql
SELECT
    COALESCE(1 + 1, 1/0)
FROM
    dual;
```

이 예제에서 COALESCE() 함수는 첫 번째 표현식의 결과가 2(1+1)이기 때문에 첫 번째 표현식만 평가했다. 두 번째 표현식(1/0)을 평가하지 않았다. 만약 그렇게 했다면 Oracle은 0으로 나누기 오류를 발생했을 것이다.

## 2. COALESCE() 예제
테스트를 위한 샘플 테이블이다.

직장 전화, 집 전화 및 휴대폰 등 다양한 전화 번호를 가진 직원의 비상 연락처를 기록해야 한다고 가정한다. 이 작업을 수행하려면 다음과 같이 emergency_contacts라는 새 테이블을 만든다.

```sql
CREATE TABLE emergency_contacts (
    contact_id NUMBER GENERATED BY DEFAULT AS IDENTITY,
    employee_id NUMBER NOT NULL,
    first_name VARCHAR2(100) NOT NULL,
    last_name VARCHAR2(100) NOT NULL,
    relationship VARCHAR2(100),
    home_phone VARCHAR2(25),
    work_phone VARCHAR2(25),
    cell_phone VARCHAR2(25),
    PRIMARY KEY (contact_id),
    FOREIGN KEY (employee_id)
    REFERENCES employees(employee_id) ON DELETE CASCADE
);
```

다음 명령문은 일부 비상 연락처를 테이블에 삽입한다.

```sql
INSERT INTO emergency_contacts ( employee_id, first_name, last_name, relationship, home_phone, work_phone, cell_phone )
VALUES ( 1,
    'Mary',
    'Bailey',
    'Wife',
    NULL,
    '515.123.4568',
    '515.123.4569' );

INSERT INTO emergency_contacts ( employee_id, first_name, last_name, relationship, home_phone, work_phone, cell_phone )
VALUES ( 2,
    'John',
    'Rivera',
    'Husband',
    NULL,
    NULL,
    '515.123.3563' );

INSERT INTO emergency_contacts ( employee_id, first_name, last_name, relationship, home_phone, work_phone, cell_phone )
VALUES ( 3,
    'Joan',
    'Cooper',
    'Mother',
    NULL,
    NULL,
    NULL );
```

일부 대화 상대는 업무용 전화만 가지고 있는 반면, 다른 대화 상대는 집 전화와 휴대전화를 가지고 있거나 전화 번호가 전혀 없을 수도 있다.

다음 쿼리는 전화 번호가 있는 직원 및 비상 연락처를 검색한다.

```sql
SELECT
    e.first_name || ' ' || e.last_name employee,
    c.first_name || ' ' || c.last_name contact,
    relationship,
    COALESCE(home_phone, work_phone, cell_phone, 'N/A') phone
FROM
    emergency_contacts c
INNER JOIN employees e USING (employee_id);
```

이 예제에서는 COALESCE() 기능을 사용하여 집 전화, 직장 전화 또는 휴대전화를 각각 사용할 수 있는 경우 선택한다. 사용 가능한 전화번호가 없을 경우, 사용할 수 없음을 나타내는 N/A 리터럴 문자열을 반환했다.

쿼리 결과는 다음과 같다.

![coalesce-function-example](/assets/img/2021-05-10-oracle-coalesce/coalesce-function-example.png)

## 3. COALESCE() 및 CASE 표현식
여러 식에서 null을 테스트하는 경우 더 긴 CASE 식 대신 COALESCE() 함수를 사용할 수 있다. COALESCE() 함수는 null 평가를 포함하는 CASE 표현식보다 더 간결하다.

두 식에서 NULL을 확인하는 경우 COALESCE() 함수는 CASE 식과 동일하다.

예를 들어 다음과 같은 COALESCE() 함수가 있다.

```text
COALESCE(e1, e2)
```

다음과 같다.

```text
CASE
WHEN
    e1 IS NOT NULL
THEN
    e1
ELSE
    e2
END
```

마찬가지로,

```text
COALESCE(e1, e2,..., en);
```

다음과 같다.

```text
CASE
WHEN
    e1 IS NOT NULL
THEN
    e1
ELSE
    COALESCE (e2, ..., en)
END
```

## 4. COALESCE() vs. NVL()
COALESCE() 함수는 SQL ANSI-92 표준의 일부이며 NVL() 함수는 Oracle에 한정된다.

두 가지 표현의 경우 COALESCE() 함수와 NVL()은 유사한 것처럼 보이지만 구현은 다르다. 다음 설명을 참조한다.

```sql
SELECT
    COALESCE(1, NULL)
FROM
    dual;
```

```sql
SELECT
    NVL(1, NULL)
FROM
    dual;
```

두 문장은 모두 동일한 결과, 즉 하나를 반환한다.

그러나 COALESCE() 함수는 결과를 결정하기 위해 첫 번째 식만 평가하고 NVL() 함수는 두 식을 모두 평가한다.

다음 예를 살펴본다.

```sql
SELECT
    COALESCE(1,1/0)
FROM
    dual;
```

위의 문이 1을 반환했지만 다음 예제에서는 오류가 발생했다.

```sql
SELECT
    NVL(1, 1/0)
FROM
    dual;
```

오류는 다음과 같다.

```text
"ORA-01476: divisor is equal to zero"
```

NVL() 함수가 오류를 일으키는 두 번째 식 1/0을 평가했기 때문이다.

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-comparison-functions/oracle-coalesce/](https://www.oracletutorial.com/oracle-comparison-functions/oracle-coalesce/)
