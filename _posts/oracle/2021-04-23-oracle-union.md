---
title: Oracle UNION
author: dejavuhyo
date: 2021-04-23 09:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-union, union, 오라클-유니온, 유니온, 합치기]
---

## 1. Oracle UNION
UNION 연산자는 둘 이상의 SELECT 명령문의 결과 집합을 단일 결과 집합으로 결합하는 집합 연산자이다.

다음은 두 쿼리의 결과 집합을 결합하는 UNION 연산자의 구문을 보여준다.

```sql
SELECT
    column_list_1
FROM
    T1
UNION
SELECT
    column_list_1
FROM
    T2;
```

이 명령문에서는 column_list_1과 column_list_2의 열 수가 같아야 한다. 또한 해당 열의 데이터 유형은 숫자 또는 문자와 같은 데이터 유형 그룹에 속해야 한다.

기본적으로 UNION 연산자는 두 결과 집합에서 고유한 행을 반환한다. 중복 행을 유지하려면 다음과 같이 UNION ALL을 명시적으로 사용한다.

```sql
SELECT
    column_list
FROM
    T1
UNION ALL
SELECT
    column_list
FROM
    T2;
```

## 2. UNION illustration
T1과 T2의 두 가지 표가 있다고 가정한다.

* T1은 3개의 행 1, 2, 3을 가지고 있다.

* T2는 3개의 행 2, 3, 4를 가지고 있다.

다음 그림은 T1 및 T2 테이블의 UNION을 보여준다.

![union](/assets/img/2021-04-23-oracle-union/union.png)

UNION이 중복된 2행과 3행을 제거했다.

다음 그림은 UNION ALL의 T1 및 T2 결과를 보여준다.

![union-all](/assets/img/2021-04-23-oracle-union/union-all.png)

UNION ALL은 중복된 2행과 3행을 유지한다.

## 3. UNION examples
샘플 데이터베이스에서 다음 employees 및 contacts 테이블을 참조한다.

![employees-table](/assets/img/2021-04-23-oracle-union/employees-table.png)

![contacts-table](/assets/img/2021-04-23-oracle-union/contacts-table.png)

### 1) UNION example
employees contacts 테이블에서 이메일 주소로 이메일을 보내야 한다고 가정한다. 이 작업을 수행하려면 먼저 직원 및 연락처의 전자 메일 주소 목록을 작성해야 한다. 그리고 나서 그 목록에 이메일을 보낸다.

다음 명령문은 UNION을 사용하여 employees 및 contacts 테이블의 연락처 목록을 작성한다.

```sql
SELECT
    first_name,
    last_name,
    email,
    'contact'
FROM
    contacts
UNION
SELECT
    first_name,
    last_name,
    email,
    'employee'
FROM
    employees;
```

결과는 다음과 같다.

![union-example](/assets/img/2021-04-23-oracle-union/union-example.png)

### 2) UNION and ORDER BY example
UNION 반환한 결과 집합을 정렬하려면 마지막 SELECT 문에 ORDER BY 절을 추가한다.

```sql
SELECT
    first_name || ' ' || last_name name,
    email,
    'contact'
FROM
    contacts
UNION
SELECT
    first_name || ' ' || last_name name,
    email,
    'employee'
FROM
    employees
ORDER BY
    name DESC;
```

이 예에서는 성과 이름이 연결된 이름별로 목록을 정렬했다.

결과는 다음과 같다.

![union-order-by-example](/assets/img/2021-04-23-oracle-union/union-order-by-example.png)

### 3) UNION ALL example
다음 명령문은 직원 및 연락처의 고유한 성을 반환한다.

```sql
SELECT
    last_name
FROM
    employees
UNION
SELECT
    last_name
FROM
    contacts
ORDER BY
    last_name;
```

고유한 last_name을 반환한다.

그러나 쿼리에서 UNION 대신 UNION ALL을 사용하는 경우 다음과 같다.

```sql
SELECT
    last_name
FROM
    employees
UNION ALL
SELECT
    last_name
FROM
    contacts
ORDER BY
    last_name;
```

일부 행은 중복된 last_name을 반환한다.

![union-all-example](/assets/img/2021-04-23-oracle-union/union-all-example.png)

## 3. UNION vs JOIN
UNION은 결과 집합을 위에 배치하며, 이는 결과 집합을 수직으로 추가한다는 의미이다. 그러나 INNER JOIN 또는 LEFT JOIN과 같은 조인(join)은 결과 집합을 수평으로 결합한다.

![union-vs-inner-join](/assets/img/2021-04-23-oracle-union/union-vs-inner-join.png)

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-basics/oracle-union/](https://www.oracletutorial.com/oracle-basics/oracle-union/)
