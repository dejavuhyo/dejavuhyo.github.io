---
title: PostgreSQL 페이징
author: dejavuhyo
date: 2020-12-17 09:15:00 +0900
categories: [Database, PostgreSQL]
tags: [pagination, paging, postgresql-pagination, limit-offset, postgresql-페이징]
---

## 1. 개념
PostgreSQL에서 OFFSET, LIMIT 및 선택적으로 FETCH NEXT 연산자를 사용하여 페이징을 수행한다.

* [PostgreSQL Documentation](https://www.postgresql.org/docs/12/queries-limit.html) 참고

## 2. 페이징 처리

### 1) 샘플 데이터

```sql
CREATE TABLE customer
(
    customer_id INT NOT NULL,
    first_name VARCHAR(40) NOT NULL,
    last_name VARCHAR(20) NOT NULL,
    company VARCHAR(80),
    address VARCHAR(70),
    city VARCHAR(40),
    state VARCHAR(40),
    country VARCHAR(40),
    postal_code VARCHAR(10),
    phone VARCHAR(24),
    fax VARCHAR(24),
    email VARCHAR(60) NOT NULL,
    support_rep_id INT,
    CONSTRAINT pk_customer PRIMARY KEY (customer_id)
);
```

### 2) LIMIT
LIMIT는 원하는 레코드 수를 쿼리에 알려준다.

> LIMIT를 사용할 때 결과 행을 고유한 순서로 제한하는 ORDER BY 절을 사용하는 것이 중요하다.
>
> 그렇지 않으면 쿼리 행의 예측할 수 없는 하위 집합이 생성된다.
>
> 10번째에서 20번째 행을 요구할 수 있지만 10번째에서 20번째 행의 순서는 알 수 없다.
>
> 즉, ORDER BY를 지정하지 않으면 순서를 알 수 없다.

처음 5개의 레코드를 가져오는 쿼리 이다.

```sql
select * from public.customer
order by customer_id
limit 5;
```

### 3) OFFSET
OFFSET은 쿼리를 시작할 위치를 알려준다.

OFFSET으로 처음 5개 레코드를 가져오는 쿼리 이다. 처음부터 쿼리를 시작하도록 명시적으로 지시하고 있다.

```sql
select * from public.customer
order by customer_id
limit 5
offset 0;
```

OFFSET은 0을 기준으로 하기 때문에 1에서 시작하지 않는다.

6~10 명의 레코드를 가져오는 쿼리 이다.

```sql
select * from public.customer
order by customer_id
limit 5
offset 5;
```

OFFSET을 사용하여 쿼리를 시작할 위치를 알려준다. 0이 기준이기 때문에 6이 아닌 5를 사용한다.

### 3. 활용

* 5개의 레코드를 보여주는 1번 페이지 쿼리

```sql
select * from public.customer
order by customer_id
limit 5
offset (1 - 1) * 5;
```

* 10개의 레코드를 보여주는 2번 페이지 쿼리

```sql
select * from public.customer
order by customer_id
limit 10
offset (2 - 1) * 10;
```

## 출처 및 참고
* <https://levelup.gitconnected.com/creating-a-data-pagination-function-in-postgresql-2a032084af54>
* <https://www.postgresql.org/docs/12/queries-limit.html>
