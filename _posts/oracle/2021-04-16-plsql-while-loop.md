---
title: PL/SQL WHILE LOOP
author: dejavuhyo
date: 2021-04-16 06:05:00 +0900
categories: [Database, Oracle]
tags: [while-loop, plsql-while-loop, while-루프, plsql-while-루프]
---

## 1. Syntax
다음은 WITH 루프 문에 대한 구문이다.

```sql
WHILE condition
LOOP
    statements;
END LOOP;
```

WHILE의 조건은 TRUE, FALSE 또는 NULL로 평가하는 Boolean 식이다.

WHILE 루프 문은 WHILE 절의 조건이 TRUE로 평가되는 한 LOOP와 END LOOP 사이의 문을 계속 실행한다.

PL/SQL은 각 루프 반복 전에 WHILE 절의 조건을 평가한다. 조건이 TRUE이면 루프 본체가 실행된다. FALSE 또는 NULL일 경우 루프가 종료된다.

루프에 진입하기 전에 조건이 FALSE이면 WHILE 루프는 전혀 실행되지 않습니다. 이 동작은 루프 본문이 항상 한 번 실행되는 LOOP 문과 다르다.

루프를 조기에 종료하려면 EXIT 또는 EXIT WHEN 명령문을 사용한다.

## 2. WHILE loop examples
WHITH 루프 문을 사용하여 작동 방식을 살펴보는 몇 가지 예이다.

### 1) 단순 WHILE 루프 예제
다음 예에서는 WHILE 루프 문을 사용하는 방법을 보여 준다.

```sql
DECLARE
    n_counter NUMBER := 1;
BEGIN
    WHILE n_counter <= 5
    LOOP
        DBMS_OUTPUT.PUT_LINE( 'Counter : ' || n_counter );
        n_counter := n_counter + 1
    END LOOP;
END;
```

결과는 다음과 같다.

```text
Counter : 1
Counter : 2
Counter : 3
Counter : 4
Counter : 5
```

이 예에서는 다음을 수행한다.

* **첫째:** 카운터가 0으로 초기화되었다.

* **둘째:** WHILE 절의 조건은 각 루프 반복 전에 평가되었다.

* **셋째:** 루프 본체 내부에서는 반복할 때마다 카운터가 하나씩 증가했다. 다섯 번 반복한 후 조건은 FALSE로 루프가 종료되는 원인이다.

### 2) EXIT WHEN 문에 의해 종료된 WHILE 루프 예제
다음 예는 추가 EXIT WHEN 문이 있다는 점을 제외하면 위의 예와 동일하다.

```sql
DECLARE
    n_counter NUMBER := 1;
BEGIN
    WHILE n_counter <= 5
        LOOP
            DBMS_OUTPUT.PUT_LINE( 'Counter : ' || n_counter );
            n_counter := n_counter + 1;
            EXIT WHEN n_counter = 3;
        END LOOP;
    END;
```

결과는 다음과 같다.

```text
Counter : 1
Counter : 2
```

카운터가 3일 때 EXIT WHEN 절이 true로 평가되는 조건이다. 루프 본체는 종료되기 전에 두 번만 실행되었다.

## [출처 및 참고]
* <https://www.oracletutorial.com/plsql-tutorial/plsql-while-loop/>
