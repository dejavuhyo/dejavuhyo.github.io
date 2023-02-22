---
title: Oracle TO_CHAR
author: dejavuhyo
date: 2021-04-02 08:30:00 +0900
categories: [Database, Oracle]
tags: [oracle-to-char, to-char, 오라클-to-char]
---

## 1. TO_CHAR란
Oracle TO_CHAR() 함수는 DATE 또는 Interval 값을 지정된 날짜 형식의 문자열로 변환한다.

Oracle TO_CHAR() 기능은 쿼리에 의해 반환된 내부 날짜 데이터를 특정 날짜 형식으로 포맷하는 데 매우 유용하다.

## 2. Syntax
TO_CHAR() 함수의 구문이다.

```text
TO_CHAR(expr [, date_format] [, nslparam]);
```

## 3. Arguments
Oracle TO_CHAR()은 다음 세 가지 인수를 허용한다.

### 1) expr
expr은 변환해야 하는 DATE 또는 INTERval 값이다.

expr의 데이터 유형은 DATE,  TIMESTAMP, TIMESTAMP WITH TIME ZONE 또는 TIMESTAMP WITH LOCAL TIME ZONE가 될 수 있다.

### 2) date_format
date_format은 결과 문자열이 있어야 하는 형식을 결정하는 문자열이다.

date_format 인수는 선택 사항이다. 생략하면 TO_CHAR() 함수는 DATE 값에 대한 기본 날짜 형식, TIMESTAMP 및 TIMESTAMP WITH TIME ZONE 값에 대한 기본 타임스탬프 형식, TIMESTAP WITH TIMEZON 값에 대한 표준 타임스탬프 형식을 사용한다.

### 3) nlsparam
nlsparam 인수는 결과 문자열에서 Monday, Mon, January, Jan 등 이름 및 약어에 대한 언어를 지정한다.

nlsparam 인수의 형식은 다음과 같다.

```text
'NLS_DATE_LANGUAGE = language'
```

nlsparam 인수도 선택 사항이다. 생략하면 'TO_CHAR()' 함수는 기본 날짜 언어를 사용한다.

## 4. Return value
Oracle TO_CHAR() 함수는 지정된 형식으로 DATE 또는 Interval 값을 나타내는 문자열을 반환한다.

## 5. Examples

### 1) 현재 시스템 날짜 변환
현재 시스템 날짜를 YYYY-MM-DD 형식의 문자열로 변환

* SQL

```sql
SELECT
    TO_CHAR( sysdate, 'YYYY-MM-DD' )
FROM
    dual
```

* 결과

```text
2021-04-02
```

### 2) 현재 시스템 날짜를 긴 날짜 문자열로 변환하려면 DL 날짜 형식을 사용

* SQL

```sql
SELECT
    TO_CHAR( sysdate, 'DL' )
FROM
    dual
```

* 결과

```text
Friday, April 02, 2021
```

### 3) 다른 언어로 일 및 달의 이름을 표시하려면 nlsparam 인수를 사용

* SQL

```sql
SELECT
    TO_CHAR( sysdate, 
    'DL' , 
    'NLS_DATE_LANGUAGE = KOREAN')
FROM
    dual
```

* 결과

```text
금요일, 4월 02, 2021
```

### 4) 간격 형식 지정
TO_CHAR() 함수를 사용하여 간격을 형식화한다.

* SQL

```sql
SELECT 
    TO_CHAR(INTERVAL '600' SECOND, 'HH24:MM') result
FROM
    DUAL
```

* 결과

```text
+00 00:10:00.000000
```

### 5) 테이블 데이터의 날짜 형식 지정

* 샘플 테이블

![employees-table](/assets/img/2021-04-02-oracle-to-char/employees-table.png)

TO_CHAR() 기능을 사용하여 2016년에 입사한 분기를 반환

* SQL

```sql
SELECT
    first_name,
    last_name,
    TO_CHAR( hire_date, 'Q' ) joined_quarter
FROM
    employees
WHERE
    hire_date BETWEEN DATE '2016-01-01' AND date '2016-12-31'
ORDER BY
    first_name, last_name
```

* 결과

![to-char-function-example](/assets/img/2021-04-02-oracle-to-char/to-char-function-example.png)

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-date-functions/oracle-to_char/](https://www.oracletutorial.com/oracle-date-functions/oracle-to_char/)
