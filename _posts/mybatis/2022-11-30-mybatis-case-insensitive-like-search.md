---
title: MyBatis 대소문자 구분 없이 like 검색
author: dejavuhyo
date: 2022-11-30 21:20:00 +0900
categories: [Framework, MyBatis]
tags: [mybatis-like, like-search, mybatis-like-search, like-upper, like-lower, mybatis-upper-lower, like-검색]
---

## 1. SQL문

### 1) UPPER

```sql
SELECT *
FROM 테이블명
WHERE 컬럼 LIKE UPPER(검색어)
```

### 2) LOWER

```sql
SELECT *
FROM 테이블명
WHERE 컬럼 LIKE LOWER(검색어)
```

## 2. MyBatis 설정

### 1) Oracle

```text
column like '%' || UPPER(#{keyword}) || '%'
```

### 2) PostgreSQL, MySQL

```text
column like CONCAT('%', UPPER(#{keyword}), '%')
```

### 3) MS-SQL

```text
column like '%' + UPPER(#{keyword}) + '%'
```
