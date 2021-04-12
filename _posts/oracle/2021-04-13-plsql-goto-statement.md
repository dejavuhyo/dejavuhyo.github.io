---
title: PL/SQL GOTO Statement
author: dejavuhyo
date: 2021-04-13 06:10:00 +0900
categories: [Database, Oracle]
tags: [goto-statement, goto, pssql-goto]
---

## 1. Introduction
GOTO 문을 사용하면 레이블이 지정된 블록 또는 문으로 컨트롤을 전송할 수 있다. 다음은 GOTO 문의 구문을 보여준다.

```sql
GOTO label_name;
```
label_name은 대상 문을 식별하는 레이블의 이름이다. 프로그램에서는 아래와 같이 라벨 이름을 이중으로 둘러싸는 angle brackets으로 둘러싼다.

```text
<<label_name>>;
```

PL/SQL은 GOTO 문을 만나면 레이블 뒤에 있는 첫 번째 실행 가능 문으로 제어를 전송한다.

## 2. GOTO statement example
GOTO 문을 사용한 예이다.

```sql
BEGIN
    GOTO second_message;

    <<first_message>>
    DBMS_OUTPUT.PUT_LINE( 'Hello' );
    GOTO the_end;

    <<second_message>>
    DBMS_OUTPUT.PUT_LINE( 'PL/SQL GOTO Demo' );
    GOTO first_message;

    <<the_end>>
    DBMS_OUTPUT.PUT_LINE( 'and good bye...' );

END;
````

결과는 다음과 같다.

```text
PL/SQL GOTO Demo
Hello
and good Bye...
```

블록의 순서를 자세히 설명한다.

* __첫째:__ GOTO second_message 문이 발생하므로 컨트롤은 second_message label 뒤에 있는 문으로 전달된다.

* __둘째:__ GOTO first_message가 발생하므로 컨트롤은 first_message label 다음에 문으로 전송된다.

* __셋째:__ GOTO the_end에 도달하여 the_end 라벨 뒤에 있는 문으로 컨트롤이 전달된다.

그림은 시퀀스를 보여준다.

![goto-example](/assets/img/2021-04-13-plsql-goto-statement/goto-example.png)

## 3. GOTO statement restrictions
GOTO 문에는 다음과 같은 제한이 적용된다.

* __첫째:__ GOTO 문을 사용하여 컨트롤을 IF, CASE 또는 LOOP 문으로 전송할 수 없다.

다음 예에서는 GOTO 문을 사용하여 IF 문으로 컨트롤을 전송하려고 한다.

```sql
DECLARE 
    n_sales NUMBER;
    n_tax NUMBER;
BEGIN 
    GOTO inside_if_statement;
    IF n_sales > 0 THEN
        <<inside_if_statement>>
        n_tax := n_sales * 0.1;
    END IF;
END;
```

Oracle에서 다음 오류를 발생시켰다.

```text
PLS-00375: illegal GOTO statement; this GOTO cannot branch to label 'INSIDE_IF_STATEMENT'
```

* __둘째:__ GOTO 문을 사용하여 IF 문의 한 절에서 다른 절로 제어권을 전송할 수 없는데, 예를 들어 IF 문에서 ELSIF 또는 ELSE 절로, 또는 CASE에서 WHEN 절이다.

다음 예에서는 IF 문의 절에 컨트롤을 전달하려고 한다.

```sql
DECLARE
    n_sales NUMBER;
    n_commission NUMBER;
BEGIN
    n_sales := 120000;
    IF n_sales > 100000 THEN
        n_commission := 0.2;
        GOTO zero_commission;
    elsif n_sales > 50000 THEN
        n_commission := 0.15;
    elsif n_sales > 20000 THEN
        n_commission := 0.1;
    ELSE
        <<zero_commission>>
        n_commission := 0;
    END IF;
END;
```

Oracle에서 다음 오류를 발생시켰다.

```text
PLS-00375: illegal GOTO statement; this GOTO cannot branch to label 'ZERO_COMMISSION'
```

* __셋째:__ GOTO 문을 사용하여 하위 프로그램이나 예외 처리로 제어 권한을 전송할 수 없다.

* __넷째:__ GOTO 문을 사용하여 예외 처리에서 현재 블록으로 다시 제어 권한을 전송할 수 없다.

## [출처 및 참고]
* <https://www.oracletutorial.com/plsql-tutorial/plsql-goto/>
