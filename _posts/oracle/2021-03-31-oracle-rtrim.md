---
title: Oracle RTRIM
author: dejavuhyo
date: 2021-03-31 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-rtrim, rtirm, 오라클-rtrim]
---

## 1. RTRIM이란
Oracle RTRIM() 함수는 문자열의 오른쪽 끝에서 지정된 집합에 나타나는 모든 문자를 제거한다.

## 2. Syntax

```text
RTRIM(trim_source,[set])
```
## 3. Arguments
RTRIM() 함수는 다음 두 인수를 사용한다.

### 1) trim_source
집합에 나타나는 문자를 제거할 문자열이다.

### 2) set
trim_source 문자열의 오른쪽 끝에서 제거해야 하는 하나 이상의 문자이다.

집합 인수는 선택사항이다. RTRIM() 함수를 호출할 때 생략하면 기본적으로 단일 공백으로 설정된다. 즉, RTRIM() 기능은 기본적으로 trim_source의 오른쪽 끝에서 공백을 제거한다.

trim_source와 set의 데이터 타입은 CHAR, VARCAR2, NCARCAR2, NCLOB 중 하나가 될 수 있다.

## 4. Return Value
RTRIM() 함수는 문자열의 오른쪽 끝에서 집합에 나타나는 모든 문자가 포함된 문자열을 반환한다.

반환된 결과 문자열의 데이터 유형은 source_string이 문자 값인 경우 VARCAR2이고 source_string이 LOB 값인 경우 LOB이다.

## 5. Examples

### 1) 문자열 'ABC'의 오른쪽 끝에 있는 모든 공백을 제거

* SQL

```sql
SELECT
    RTRIM( 'ABC  ' )
FROM
    dual
```

* 결과

```text
'ABC'
```

### 2) 모든 문자열 '345' 제거

* SQL

```sql
SELECT
    RTRIM( 'ABC12345543', '345' )
FROM
    dual
```

* 결과

```text
ABC12
```

집합의 모든 문자('3', '4', '5')에 대해, RTRIM() 함수는 문자열에서 각각의 가장 오른쪽 발생을 제거했다.

### 3) 이름이 V1, V2 등으로 끝나는 제품을 반환

* 샘플 Table

![products-table](/assets/img/2021-03-31-oracle-rtrim/products-table.png)

* SQL

```sql
SELECT
    product_name
FROM
    products
WHERE
    product_name LIKE '%V_'
ORDER BY
    product_name
```

* 결과

![rtrim-product-names](/assets/img/2021-03-31-oracle-rtrim/rtrim-product-names.png)

### 4) 제품 이름에서 버전(예: V1, V2 등)을 제거

* SQL

```sql
SELECT
    product_name,
    RTRIM(product_name,'V12345679') short_name
FROM
    products
WHERE
    product_name LIKE '%V_'
ORDER BY
    product_name
```

* 결과

![rtrim-example](/assets/img/2021-03-31-oracle-rtrim/rtrim-example.png)

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-string-functions/oracle-rtrim/>
