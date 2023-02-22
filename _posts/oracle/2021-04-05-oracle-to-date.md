---
title: Oracle TO_DATE
author: dejavuhyo
date: 2021-04-05 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-to-date, to-date, 오라클-to-date]
---

## 1. TO_DATE란
Oracle TO_DATE() 함수는 날짜 리터럴을 DATE 값으로 변환한다.

## 2. Syntax
Oracle TO_DATE() 기능의 구문을 보여 준다.

```text
TO_DATE (string, format, nls_language)
```

## 3. Arguments
TO_DATE() 함수는 다음 세 가지 인수를 허용한다.

### 1) string
DATE 값으로 변환되는 문자열 값이다. 데이터 유형 CHAR, VARCAR2, NCHAR 또는 NVARCAR2의 값이 될 수 있다.

### 2) format
문자열의 날짜 및 시간 형식이다.

형식 인수는 선택 사항이다. 형식을 생략하는 경우 문자열은 표준 날짜 형식(예: 31-DEC-2000)이어야 한다.

형식이 J(Julian)이면 string은 정수여야 한다.

### 3) nls_language
문자열의 일 및 월 이름에 대한 언어를 지정하는 식이다. nls_language 인수의 형식은 다음과 같다.

```text
NLS_DATE_LANGUAGE = language
```

ls_language 인수는 선택 사항이다. 생략하면 TO_DATE() 함수가 세션에 기본 언어를 사용한다.

## 4. Return value
TO_DATE() 함수는 입력 문자열에 해당하는 DATE 값을 반환한다.

## 5. Examples

### 1) 기본 TO_DATE() 함수 예제
문자열을 날짜로 변환하려면 Oracle 날짜 형식 요소(예: 4자리 연도에는 YYYY, 2자리 월에는 MM)를 사용한다.

```text
'5 Apr 2021'
```

문자열을 DATE 값으로 변환하려면 먼저 문자열의 날짜 형식 모델을 구성해야 한다. 이 경우 5는 DD, 4월은 Mon, 2021은 YYYY로 대표되며, 그 결과 다음과 같은 형식이 된다.

```text
'DD Mon YYYY'
```

날짜 리터럴 및 형식 모델 두 인수를 TO_DATE() 함수에 전달한다.

```sql
SELECT
    TO_DATE( '5 Apr 2021', 'DD MON YYYY' )
FROM
    dual
```

다음은 결과를 보여 준다.

```text
05-APR-21
```

형식을 생성하지 못하면 TO_DATE() 함수가 각각 오류를 발생시킨다.

날짜 형식 모델에서 Y 문자가 한 개 없는 경우

```sql
SELECT
    TO_DATE( '5 Apr 2021', 'DD MON YYY' )
FROM
    dual
```

TO_DATE() 함수가 다음 오류를 발생시켰다.

```text
ORA-01830: date format picture ends before converting entire input string
```

### 2) TO_DATE() 함수를 사용하여 테이블에 데이터 삽입
멤버 테이블을 생성한다.

```sql
CREATE TABLE members (
    first_name VARCHAR2 ( 50 ),
    last_name VARCHAR2 ( 50 ),
    joined_date DATE
)
```

구성원 테이블에 새 행을 삽입한다.

```sql
INSERT INTO members(first_name, last_name, joined_date)
VALUES('Laureen','Davidson', TO_DATE('Apr 05 2021','Mon DD YYYY'))
```

2021년 4월 5일이 Oracle 표준 날짜 형식이 아니므로 테이블에 저장하기 전에 TO_DATE() 함수를 사용하여 이를 DATE 값으로 변환해야 한다.

형식 지정에 TO_DATE() 함수를 사용하여 날짜 값을 DATE 열에 삽입하는 다른 예이다.

```sql
INSERT INTO members(first_name, last_name, joined_date)
VALUES('Thomas','Phelps', TO_DATE('05 April 2021','DD Month YYYY'))
```

다음 쿼리를 사용하여 데이터가 실제로 삽입되었는지 여부를 확인할 수 있다.

```sql
SELECT
    *
FROM
    members
```

![to-date-function-example](/assets/img/2021-04-05-oracle-to-date/to-date-function-example.png)

## [출처 및 참고]
* [https://www.oracletutorial.com/oracle-date-functions/oracle-to_date/](https://www.oracletutorial.com/oracle-date-functions/oracle-to_date/)
