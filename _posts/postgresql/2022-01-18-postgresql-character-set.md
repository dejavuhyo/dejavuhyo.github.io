---
title: PostgreSQL 인코딩 확인
author: dejavuhyo
date: 2022-01-18 09:20:00 +0900
categories: [Database, PostgreSQL]
tags: [postgresql-character-set, character-set, postgresql-인코딩, 인코딩]
---

## 1. 클라이언트 인코딩 확인

```sql
show client_encoding;
```

## 2. 서버 인코딩 확인

```sql
show server_encoding;
```

## 3. Database 인코딩 확인

### 1) 특정 DB

```sql
select pg_encoding_to_char(encoding) from pg_database where datname = 'postgres';
```

![one-db](/assets/img/2022-01-18-postgresql-character-set/one-db.png)

### 2) 전체 DB

```sql
select *, pg_encoding_to_char(encoding) from pg_database;
```

![all-db](/assets/img/2022-01-18-postgresql-character-set/all-db.png)

## [출처 및 참고]
* <https://cbwstar.tistory.com/entry/PostgreSQL-%EC%9D%B8%EC%BD%94%EB%94%A9encoding-%ED%99%95%EC%9D%B8>
