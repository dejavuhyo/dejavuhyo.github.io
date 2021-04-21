---
title: Oracle INTERSECT
author: dejavuhyo
date: 2021-04-22 07:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-intersect, intersect, 오라클-인터섹트, 인터섹트, 교차]
---

## 1. Oracle INTERSECT
Oracle INTERSECT 연산자는 두 쿼리의 결과를 비교하고 두 쿼리에 의해 출력되는 고유한 행을 반환한다.

다음 문에는 INTERSECT 연산자의 구문을 보여준다.

```sql
SELECT
    column_list_1
FROM
    T1
INTERSECT 
SELECT
    column_list_2
FROM
    T2;
```

UNION 연산자와 마찬가지로 INTERSECT 연산자를 사용할 때는 다음 규칙을 따라야 합니다.

* 열 수와 순서는 두 쿼리에서 동일해야 한다.

* 해당 열의 데이터 유형은 숫자 또는 문자와 같은 데이터 유형 그룹에 속해야 한다.

## 2. INTERSECT illustration
T1과 T2 결과 집합을 반환하는 두 개의 쿼리가 있다고 가정한다.

* T1 result set은 1, 2, 3이 포함된다.

* T2 result set은 2, 3, 4가 포함된다.

T1과 T2 결과의 교차점은 2와 3을 반환한다. 이러한 값은 두 쿼리에 의해 출력되는 고유한 값이기 때문이다.

다음 그림은 T1과 T2의 교차점을 보여준다.

![intersect](/assets/img/2021-04-22-oracle-intersect/intersect.png)

그림에서는 교차점이 두 원(또는 세트)의 교차점을 반환한다는 것을 보여 준다.

## 3. INTERSECT example
샘플 데이터베이스에서 contacts 및 employees table을 참조한다.

![contacts-table](/assets/img/2021-04-22-oracle-intersect/contacts-table.png)

![employees-table](/assets/img/2021-04-22-oracle-intersect/employees-table.png)

다음 명령문은 contacts 및 employees table의 사용자가 사용하는 성을 가져오기 위해 INTERSECT 연산자를 사용한다.

```sql
SELECT
    last_name
FROM
    contacts
INTERSECT 
SELECT
    last_name
FROM
    employees
ORDER BY
    last_name;
```

![intersect-example](/assets/img/2021-04-22-oracle-intersect/intersect-example.png)

우리는 마지막 쿼리에 ORDER BY 절을 배치하여 INTERSECT 연산자가 반환한 결과 집합을 정렬한다는 점에 유의한다.

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-basics/oracle-intersect/>
