---
title: PL/SQL CONTINUE
author: dejavuhyo
date: 2021-04-19 06:30:00 +0900
categories: [Database, Oracle]
tags: [continue, plsql-continue, continue-when, plsql-continue-when]
---

## 1. CONTINUE statement
CONTINE 문을 사용하면 현재 루프 반복을 종료하고, 즉시 해당 루프의 다음 반복을 계속할 수 있다.

CONTINUE 문에는 간단한 구문이 있다.

```sql
CONTINUE;
```

일반적으로 CONTEN 문은 IF THEN 문 내에서 지정된 조건에 따라 현재 루프 반복을 종료하는 데 사용된다.

```sql
IF condition THEN
    CONTINUE;
END IF;
```

CONTINE은 LOOP, FOR LOOP 및 WHILE LOOP를 포함한 모든 루프 구조에서 사용될 수 있다.

## 2. CONTINUE statement example
다음은 CONTINE 문을 사용하여 홀수에 대한 루프 본문 실행을 건너뛰는 간단한 예제이다.

```sql
BEGIN
    FOR n_index IN 1 .. 10
    LOOP
        -- skip odd numbers
        IF MOD( n_index, 2 ) = 1 THEN
            CONTINUE;
        END IF;
        DBMS_OUTPUT.PUT_LINE( n_index );
    END LOOP;
END;
```

결과는 다음과 같다.

```text
2
4
6
8
10
```

## 3. CONTINUE WHEN statement
CONTINUE WHEN 문이 조건에 따라 현재 루프 반복을 종료하고 즉시 해당 루프의 다음 반복을 계속한다.

CONTINUE WHEN 문 구문은 다음과 같다.

```sql
CONTINUE WHEN condition;
```

WHEN 절의 조건은 CONTINUE WHEN 문에 도달할 때마다 평가된다. 조건이 TRUE이면 현재 루프는 건너뛰고 컨트롤은 루프의 다음 반복으로 전송된다. 조건이 TRUE가 아닌 경우 CONTINUE WHEN 문은 아무 동작도 하지 않는다.

기본적으로 CONTINUE WHEN 문장은 IF THEN 문과 CONTINUE 문장의 조합이다.

```sql
IF condition THEN
    CONTINUE;
END IF;
```

CONTINUE 문과 마찬가지로 LOOP, FOR LOOP 및 WHILE LOOP에서 CONTINUE WHEN 문을 사용할 수 있다.

## 4. CONTINUE statement example
CONTINUE WHEN 문을 사용하여 짝수에 대한 루프 본문 실행을 건너뛰는 방법을 보여 준다.

```sql
BEGIN
    FOR n_index IN 1 .. 10
    LOOP
        -- skip even numbers
        CONTINUE
    WHEN MOD( n_index, 2 ) = 0;
        DBMS_OUTPUT.PUT_LINE( n_index );
    END LOOP;
END;
```

결과는 다음과 같다.

```text
1
3
5
7
9
```

## [출처 및 참고]
* <https://www.oracletutorial.com/plsql-tutorial/plsql-continue/>
