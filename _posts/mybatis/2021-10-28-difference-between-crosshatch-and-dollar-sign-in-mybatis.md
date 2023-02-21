---
title: MyBatis 크러스햇지(#{})와 달러 사인(${})의 차이점
author: dejavuhyo
date: 2021-10-28 06:00:00 +0900
categories: [DevOps, MyBatis]
tags: [crosshatch-dollar-sign, crosshatch-dollar-sign-mybatis, 크러스햇지-달러-사인, 크러스햇지-달러-사인-차이점, mybatis-크러스햇지-달러-사인]
---

## 1. 파라미터 문법
MyBatis에서 mapper 쿼리문이 있는 XML 파일에 파라미터 값을 설정할 때 크러스햇지(`#{}`)와 달러 사인(`${}`) 기호를 사용한다. 이 둘은 용도가 다르기 때문에 명확히 구분해서 사용해야 한다.

## 2. 차이점

### 1) 크러스햇지(#{})
크러스햇지(`#{}`)는 값에 작은따옴표(`'`)가 붙으며, PreparedStatement가 String 형태로 들어와 자동으로 parameter 형식이 된다. [SQL injection](https://dejavuhyo.github.io/posts/mybatis-data-map/)을 예방할 수 있기 때문에 보안에 유리하다.

* MyBatis Mapper

```sql
SELECT name FROM user_tb WHERE user_id = #{id}
```

* Query

```sql
SELECT name FROM user_tb WHERE user_id = ?
```

* 수행 Query

```sql
SELECT name FROM user_tb WHERE user_id = 'admin'
```

### 2) 달러 사인(${})
달러 사인(`${}`)은 값에 작은따옴표(`'`)가 붙지 않으며, Statement의 Parameter 값을 그대로 전달되기 때문에 의도적인 쿼리 주입을 막을 수 없다.

* MyBatis Mapper

```sql
SELECT name FROM user_tb WHERE user_age = ${age}
```

* Query

```sql
SELECT name FROM user_tb WHERE user_age = 30
```

* 수행 Query

```sql
SELECT name FROM user_tb WHERE user_age = 30
```

## [출처 및 참고]
* [https://developing-move.tistory.com/4](https://developing-move.tistory.com/4)
