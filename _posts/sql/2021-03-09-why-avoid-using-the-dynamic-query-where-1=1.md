---
title: 동적 쿼리 WHERE 1=1 사용을 지양하는 이유
author: dejavuhyo
date: 2021-03-18 06:05:00 +0900
categories: [Database, SQL]
tags: [sql-where, where-1-1, dynamic-query-where, avoid-where-1-1, where-1-1-사용지양]
---

## 1. WHERE 1=1 이란
WHERE 1=1은 항상 참을 의미한다. 즉, 의미 없는 조건이다.

## 2. 사용 이유
주석처리가 편하며, WHERE 문을 유동적으로 작성해야 할 때 편하다. WHERE 1=1을 사용하면 AND 조건을 사용할 때 WHERE 문을 신경 쓰지 않고 AND 조건문을 편리하게 사용할 수 있다.

## 3. 잠재적인 위험성

### 1) 회원 정보 1명을 조회하는 쿼리

* SELECT 문

```xml
<select id="select">
    SELECT
        id, name, email, address
    FROM member
    WHERE 1=1
    <if test="name != null and name != ''">
        AND id = #{id}
        AND name = #{name}
    </if>
</select>
```

* id와 name의 값이 null이면 만들어지는 SQL문

```sql
SELECT
    id, name, email, address
FROM member
WHERE 1=1
```

모든 member 정보를 조회하는 쿼리이기 때문에 여러개의 행이 조회 된다. SELECT 문이기 때문에 큰 문제는 없지만, UPDATE, DELETE 문에서는 심각한 문제가 발생한다.

### 2) 회원 정보 1명을 수정하는 쿼리

* DELETE 문

```xml
<update id="update">
    UPDATE member
    SET use_yn = 'N'
    WHERE 1=1
    <if test="id != null">
        AND id = #{id}
    </if>
</update>
```

* id의 값이 null이면 만들어지는 SQL문

```sql
UPDATE member
SET use_yn = 'N'
WHERE 1=1
```

모든 member의 use_yn이 N으로 업데이트 되는 문제가 발생한다.

> null 체크는 당연히 해야되는 것이다. 그러나 사람은 실수를 하기 때문에 문제가 발생한다. 애초에 WHERE 1=1이라는 구문을 사용하지 않는 것이 잠재적인 오류를 없애는 방법일 것이다.

## [출처 및 참고]
* <https://okky.kr/article/250963>
* <https://jdm.kr/blog/7>
