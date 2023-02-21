---
title: MyBatis Like 검색 구문
author: dejavuhyo
date: 2021-06-15 06:30:00 +0900
categories: [DevOps, MyBatis]
tags: [mybatis-like-syntax, mybatis-like, mybatis-like-검색, mybatis-like-검색-구문]
---

## 1. Like 구문

### 1) Oracle

```text
column like '%' || #{keyword} || '%'
```

### 2) PostgreSQL, MySQL

```text
column like CONCAT('%', #{keyword}, '%')
```

### 3) MS-SQL

```text
title like '%' + #{keyword} + '%'
```

## [출처 및 참고]
* [https://fruitdev.tistory.com/60](https://fruitdev.tistory.com/60)
