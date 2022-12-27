---
title: Oracle REGEXP_REPLACE
author: dejavuhyo
date: 2021-04-08 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-regexp-replace, regexp-replace, 오라클-regexp-replace]
---

## 1. Overview
Oracle REGEXP_REPLACE() 함수는 정규식 패턴을 다른 문자열과 일치시키는 일련의 문자를 대체한다.

REGEXP_REPLACE() 함수는 REPLACE() 함수의 고급 버전이다.

## 2. Syntax
Oracle REGEXP_REACH() 기능의 구문이다.

* SQL

```sql
REGEXP_REPLACE ( source_string, search_pattern
    [, replacement_string
        [, star_position
            [, nth_occurrence
                [, match_parameter ]
            ]
        ]
    ]
)
```

## 3. Arguments
REGEXP_REACH() 함수는 6개의 인수를 사용한다.

### 1) source_string
검색할 문자열이다.

### 2) search_pattern
원본 문자열에서 검색하는 데 사용되는 정규식 패턴이다.

### 3) replacement_string
원본 문자열에서 일치하는 패턴을 대체하는 문자열이다. 이 인수는 선택 사항이며 기본값은 null이다.

### 4) start_position
검색이 시작되는 원본 문자열의 위치를 결정하는 정수이다. start_position도 선택 사항이다. 언급하지 않은 경우 start_position은 소스 문자열의 시작 위치인 1이다.

### 5) nth_occurrence
대체해야 하는 위치를 나타내는 양수가 아닌 정수이다. nth_position이 0이면, REGEXP_REPLACE() 함수가 일치 항목의 모든 항목을 대체한다. 그렇지 않으면 REGEXP_REPLACE() 함수가 n번째 발생을 대체한다.

### 6) match_parameter
함수의 기본 일치 동작을 변경하는 리터럴 문자열이다. 이 함수에서 match_parameter의 동작은 REGEXP_SUBSTR() 함수의 동작과 같다.

## 4. Return Value
REGEXP_REPLACE() 함수는 일치하는 패턴이 있는 문자열을 다른 문자열로 대체하여 반환한다.

## 5. Examples

### 1) Removing special characters from a string
데이터베이스에 특수 문자가 포함되어 있는 경우가 있다. 다음 명령문은 REGEXP_REPLACE() 함수를 사용하여 문자열에서 특수 문자를 제거한다.

* SQL

```sql
SELECT
    REGEXP_REPLACE('Th♥is∞ is a dem☻o of REGEXP_♫REPLACE function','[^a-z_A-Z ]')
FROM 
    dual
```

* 결과

```text
This is a demo of REGEXP_REPLACE function
```

이 쿼리는 데이터 정리 작업 프로세스에서 유용하다.

그림은 정규식 `[^a-z_A-Z]`의 의미를 설명한다.

![regular-expression-1](/assets/img/2021-04-08-oracle-regexp-replace/regular-expression-1.png)

### 2) Masking sensitive information
다음 문장은 보안을 위해 신용카드의 중간 부분을 숨긴다.

이 기술을 엄격한 보안이 필요한 전자상거래, 뱅킹 및 기타 금융 애플리케이션에서 적용할 수 있다.

* SQL

```sql
SELECT
    regexp_replace( '4024007187788590', '(^\d{3})(.*)(\d{4}$)', '\1**********\3' ) credit_card
FROM
    dual
```

* 결과

```text
402**********8590
```

그림은 정규식 `(^\d{3})(.*)(\d{4}$)`의 의미를 설명한다.

![regular-expression-2](/assets/img/2021-04-08-oracle-regexp-replace/regular-expression-2.png)

### 3) Removing redundant spaces
문자열에서 둘 이상 나타나는 공백 문자인 중복 공백을 제거한다.

* SQL

```sql
SELECT
    regexp_replace('This line    contains    more      than one   spacing      between      words', '( ){2,}', ' ' ) regexp_replace
FROM
    dual
```

* 결과

```text
This line contains more than one spacing between words
```

그림은 정규식 `( ){2,}`의 의미를 설명한다.

![regular-expression-3](/assets/img/2021-04-08-oracle-regexp-replace/regular-expression-3.png)

### 4) Reformat phone numbers

* 샘플 employees table

![employees-table](/assets/img/2021-04-08-oracle-regexp-replace/employees-table.png)

xxx.xxx.xxxx. 패턴과 일치하는 전화 번호를 검색하고 (xxx) xxx-xxxxx 패턴을 사용하여 다시 설정한다.

* SQL

```sql
SELECT
    first_name, 
    last_name,
    REGEXP_REPLACE( phone,  '(\d{3})\.(\d{3})\.(\d{4})',  '(\1) \2-\3' ) phone_number
FROM
    employees
ORDER BY
    phone_number
```

* 결과

![function-example](/assets/img/2021-04-08-oracle-regexp-replace/function-example.png)

그림은 정규식 `([[:digit:]]{3})\.([[:digit:]]{3})\.([[:digit:]]{4})`의 의미를 설명한다.

![regular-expression-4](/assets/img/2021-04-08-oracle-regexp-replace/regular-expression-4.png)

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-string-functions/oracle-regexp_replace/>
