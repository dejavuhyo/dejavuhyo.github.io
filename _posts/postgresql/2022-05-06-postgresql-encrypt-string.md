---
title: PostgreSQL 문자열 암호화
author: dejavuhyo
date: 2022-05-06 11:00:00 +0900
categories: [Database, PostgreSQL]
tags: [postgresql-encrypt-string, encrypt-string, postgresql-문자열-암호화, 문자열-암호화]
---

## 1. pgcrypto 플러그인
pgcrypto 플러그인을 설치한다.

```sql
CREATE EXTENSION pgcrypto;
```

## 2. 문자열 암호화
일반 문자열 암호화를 위한 `encrypt()`과 `decrypt()` 함수가 있다.

`encrypt()`와 `decrypt()` 함수는 `convert_to()`와 `convert_from()` 함수와 주로 함께 사용한다.

### 1) 암호화
문자열 인코딩 함수 `convert_to()`를 사용한다.

```sql
SELECT encode(encrypt(convert_to('암호화', 'utf8'), 'encrypt_key', 'aes'), 'hex');
```

### 2) 복호화
문자열 디코딩 함수 `convert_from()`를 사용한다.

```sql
SELECT convert_from(decrypt(decode('b7fdc764f1e95d5e4847e5572ee3f01c', 'hex'), 'encrypt_key', 'aes'), 'utf8');
```

## [출처 및 참고]
* <https://guiyomi.tistory.com/98>
