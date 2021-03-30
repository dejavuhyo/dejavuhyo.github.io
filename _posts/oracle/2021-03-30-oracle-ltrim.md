---
title: Oracle LTRIM
author: dejavuhyo
date: 2021-03-30 07:10:00 +0900
categories: [Database, Oracle]
tags: [oracle-ltrim, ltirm, 오라클-ltrim]
---

## 1. LTRIM이란
Oracle LTRIM() 함수는 문자열의 왼쪽 끝에서 집합에 포함된 모든 문자를 제거한다.

## 2. Syntax

```text
LTRIM(trim_source,[set])
```

## 3. Arguments
LTRIM() 함수는 두 가지 인수를 허용한다.

### 1) trim_source
원하지 않는 문자를 제거해야 하는 문자열이다.

### 2) set
trim_source 문자열에서 제거해야 하는 문자가 하나 이상 포함된 집합입니다.

set 인수는 선택 사항이다. 지정하지 않으면 세트는 기본적으로 단일 공백으로 설정된다.

trim_source와 set의 데이터 타입은 CHAR, VARCAR2, NCARCAR2, NCLOB, NVARCARCAR2, NCLOB 중 하나가 될 수 있다.

## 4. Return Value
LTRIM() 함수는 집합에 포함된 모든 문자가 문자열의 왼쪽 끝에서 제거되는 문자열을 반환한다.

source_string이 문자 값이면 결과 문자열에는 VARCAR2 데이터 유형이 있고 source_string이 LOB 값이면 LOB가 있다.

## 5. Examples

### 1) 문자열 '  XYZ'의 왼쪽 끝에서 모든 공백을 제거

```sql
SELECT
    LTRIM( '  XYZ' )
FROM
    dual
```

제거되어야 하는 문자 집합, LTRIM() 함수가 기본적으로 공백을 제거하도록 지정하지 않았다.

* 결과

```text
'XYZ'
```

### 2) 문자열 'XYZ123456'의 왼쪽 끝에서 (1,2,3) 집합의 문자를 제거

```sql
SELECT
    LTRIM( '123456XYZ', '123' )
FROM
    dual
```

* 결과

```text
456XYZ
```

### 3) 이름이 'ASRock'로 시작하는 제품을 반환

* 샘플 Table

![products-table](/assets/img/2021-03-30-oracle-ltrim/products-table.png)

```sql
SELECT
    product_name
FROM
    products
WHERE
    product_name LIKE 'ASRock%'
ORDER BY
    product_name
```

* 결과

![ltrim-funcion-example](/assets/img/2021-03-30-oracle-ltrim/ltrim-funcion-example.png)

LTRIM() 기능을 사용하여 제품 이름에서 'ASRock' 문자열을 제거하여 다음과 같이 짧게 할 수 있다.

```sql
SELECT
    product_name,
    LTRIM( product_name, 'ASRock' ) short_product_name
FROM
    products
WHERE
    product_name LIKE 'ASRock%'
ORDER BY
    product_name
```
* 결과

![ltrim-funcion](/assets/img/2021-03-30-oracle-ltrim/ltrim-funcion.png)

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-string-functions/oracle-ltrim/>
