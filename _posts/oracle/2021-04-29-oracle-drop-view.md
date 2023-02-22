---
title: Oracle DROP VIEW
author: dejavuhyo
date: 2021-04-29 08:30:00 +0900
categories: [Database, Oracle]
tags: [oracle-drop-view, drop-view, 오라클-drop-view, 오라클-뷰-삭제, 뷰-삭제]
---

## 1. Oracle DROP VIEW statement
데이터베이스에서 view를 제거하려면 다음 DROP VIEW 문을 사용한다.

```sql
DROP VIEW schema_name.view_name
[CASCADE CONSTRAINT];
```

### 1) schema_name
첫째, view를 포함하는 스키마의 이름을 지정한다. 스키마 이름을 건너뛰면 Oracle은 view가 사용자 자신의 스키마에 있다고 가정한다.

### 2) view_name
둘째, 삭제할 view의 이름을 지정한다. 다른 view, 구체화된 view 또는 동의어가 view를 참조하는 경우 Oracle은 이러한 개체를 무효로 표시하지만 제거하지 않는다.

### 3) CASCADE CONSTRAINT
셋째, view에 제약 조건이 있는 경우, view의 기본 키 및 고유 키를 참조하는 모든 참조 무결성 제약 조건을 삭제하려면 CASCADE CONSTRAINT 절을 지정해야 한다. 그렇지 않으면 이러한 제약 조건이 존재할 경우 DROP VIEW 문이 실패한다.

## 2. DROP VIEW examples
다음 문장은 employees 테이블을 기반으로 salesman이라는 뷰를 생성한다.

```sql
CREATE VIEW salesman AS
SELECT
    *
FROM
    employees
WHERE
    job_title = 'Sales Representative';
```

이 view는 작업 제목이 판매 담당자인 직원만 반환한다.

```sql
SELECT
    *
FROM
    salesman;
```

![salesman-view](/assets/img/2021-04-29-oracle-drop-view/salesman-view.png)

다음 문장은 salesman view를 기반으로 salesman_contacts라는 또 다른 뷰를 생성한다.

```sql
CREATE VIEW salesman_contacts AS
SELECT
    first_name,
    last_name,
    email,
phone
FROM
    salesman;
```

salesman_contacts view는 salesman 이름, 이메일 및 전화만 반환한다.

```sql
SELECT
    *
FROM
    salesman_contacts;
```

![salesman-contacts-view](/assets/img/2021-04-29-oracle-drop-view/salesman-contacts-view.png)

salesman view를 삭제하려면 다음 문구를 사용한다.

```sql
DROP VIEW salesman;
```

salesman_contacts view는 salesman view에 따라 달라지기 때문에, salesman view가 삭제되었을 때 유효하지 않게 되었다.

user_objects view에서 보기 상태를 확인할 수 있다. 개체 이름은 대문자로 입력해야 한다.

```sql
SELECT
    object_name,
    status
FROM
    user_objects
WHERE
    object_type = 'VIEW'
    AND object_name = 'SALESMAN_CONTACTS';
```

![invalid-view](/assets/img/2021-04-29-oracle-drop-view/invalid-view.png)

salesman_contacts view의 상태가 INVALID 되었다.

```sql
DROP VIEW salesman_contacts;
```

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-view/oracle-drop-view/](https://www.oracletutorial.com/oracle-view/oracle-drop-view/)
