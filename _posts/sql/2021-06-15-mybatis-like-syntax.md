---
title: MyBatis Like 검색 구문
date: 2021-06-15 06:30:00 +0900
categories: [Database, SQL]
tags: [mybatis-like-syntax, mybatis-like, mybatis-like-검색, mybatis-like-검색-구문]
---

## 1. Like 구문

### 1) Oracle

```text
column like '%' || #{keyword} || '%'
```

### 2) MySQL

```text
column like CONCAT('%', #{keyword}, '%')
```

### 3) MS-SQL

```text
title like '%' + #{keyword} + '%'
```

## [출처 및 참고]
* <https://fruitdev.tistory.com/60>
