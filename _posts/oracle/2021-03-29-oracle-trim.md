---
title: Oracle TRIM
author: dejavuhyo
date: 2021-03-29 06:05:00 +0900
categories: [Database, Oracle]
tags: [oracle-trim, tirm, 오라클-trim]
---

## 1. TRIM이란
Oracle TRIM() 기능은 문자열의 시작, 끝 또는 양쪽 끝에서 공백 또는 지정된 문자를 제거한다.

## 2. Syntax

![trim](/assets/img/2021-03-29-oracle-trim/trim.png)

```text
TRIM( [ [ LEADING | TRAILING | BOTH ] trim_character FROM ] trim_source)
```

## 3. Arguments
Oracle TRIM() 함수는 다음 세 가지 인수를 허용한다.

### 1) LEADING, TRAILING, BOTH
첫 번째 인수를 사용하면 문자열의 어느 쪽을 자를지 지정할 수 있다.

* READING은 지정된 trim_character와 동일한 선행 문자를 제거한다.

* TRACKING은 지정된 trim_character

* BOWN은 지정된 trim_character 및 동일한 선행 및 후행 문자를 모두 제거한다.

첫 번째 인수는 선택 사항이다. 명시적으로 지정하지 않으면 TRIM() 함수가 trim_source에서 선행 및 후행 문자를 모두 제거한다.

### 2) trim_character
trim_source의 선행, 후행 또는 양쪽에서 제거 하는 지정된 문자이다.

### 3) trim_source
trim_source를 제거 하는 문자열이다.

trim_carter 및 trim_source의 데이터 유형은 CHAR, VARCHAR2, NCHAR, NVARCHAR2, CLOB, or NCLOB 데이터 유형 중 하나가 될 수 있다.

## 4. Return value
TRIM() 함수는 trim_character가 trim_source의 선행, 후행 또는 양쪽 모두에서 제거되는 문자열을 반환한다.

trim_source가 문자 데이터 유형인 경우 결과 문자열의 데이터 유형은 VARCAR2 또는 trim_source가 LOB 데이터 유형인 경우 LOB이다.

결과 문자열의 문자 집합은 trim_source와 동일하다.

## 5. Examples

### 1) 'ABC' 문자열에서 선행 및 후행 공백을 모두 제거

* Query

```sql
SELECT
    TRIM( '  ABC  ' )
FROM
    dual
```

* 결과

```text
'ABC'
```

### 2) 'ABC' 문자열에서 선행 공백을 제거

* Query

```sql
SELECT
    TRIM( LEADING ' ' FROM  ' ABC ' )
FROM
    dual
```

* 결과

```text
'ABC  '
```

### 3) 'ABC' 문자열에서 후행 공백을 제거

* Query

```sql
SELECT
    TRIM( TRAILING ' ' FROM  '  ABC  ' )
FROM
    dual
```

* 결과

```text
'  ABC'
```

### 4) 숫자 문자열에서 선행 0을 제거

* Query

```sql
SELECT
    TRIM(LEADING  '0' FROM  '00012345' )
FROM
    dual
```

* 결과

```text
'12345'
```

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-string-functions/oracle-trim/>
