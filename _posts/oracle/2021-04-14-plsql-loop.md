---
title: PL/SQL LOOP
author: dejavuhyo
date: 2021-04-14 06:10:00 +0900
categories: [Database, Oracle]
tags: [loop, plsql-loop, 반복문, plsql-반복문]
---

## 1. LOOP syntax
PL/SQL LOOP 문의 구조는 다음과 같다.

```sql
<<label>> LOOP
    statements;
END LOOP loop_label;
```
이 구조는 FOR LOOP와 WHID LOOP를 포함한 모든 루프 구조 중 가장 기초적이다. 이 기본 LOOP 문은 LOOP 키워드, 실행 가능한 코드의 본문, END LOOP 키워드로 구성된다.

LOOP 문은 본문에서 문을 실행하고 제어 권한을 루프 상단에 반환한다. 일반적으로 루프의 본문에는 루프를 종료하기 위한 EXIT 또는 EXIT WHEN 문이 하나 이상 포함되어 있다. 그렇지 않으면 루프는 무한 루프가 된다.

LOOP 문은 문의 시작과 끝에 나타나는 선택적 레이블을 가질 수 있다.

다음과 같은 경우 LOOP 문을 사용하는 것이 좋다.

* 루프 본문을 한 번 이상 실행하려고 한다.

* 루프를 실행할 횟수를 확신할 수 없다.

## 2. EXIT statement
EXIT 문을 사용하면 루프의 현재 반복을 무조건 종료할 수 있다.

```sql
LOOP
    EXIT;
END LOOP;
```

일반적으로 조건이 참일 때 루프를 종료하기 위해 EXIT 문과 함께 IF 문을 사용한다.

```sql
LOOP
    IF condition THEN
        EXIT;
    END IF;
END LOOP;
```

다음 예는 LOOP 문을 사용하여 코드와 시퀀스를 실행하는 방법을 보여주고, 루프를 종료하기 위한 EXIT 명령문이다.

```sql
DECLARE
    l_counter NUMBER := 0;
BEGIN
    LOOP
        l_counter := l_counter + 1;
        IF l_counter > 3 THEN
        EXIT;
    END IF;
        dbms_output.put_line( 'Inside loop: ' || l_counter );
    END LOOP;
    -- control resumes here after EXIT
    dbms_output.put_line( 'After loop: ' || l_counter );
END;
```

다음은 결과이다.

```text
Inside loop: 1
Inside loop: 2
Inside loop: 3
After loop: 4
```

다음은 코드의 논리를 설명한다.

* __첫째:__ l_counter 변수를 0으로 선언하고 초기화한다.

* __둘째:__ 루프 내에서 l_counter를 1만큼 증가시키고 l_counter가 3보다 크면 루프를 종료한다. l_counter가 3보다 작거나 같으면 l_counter 값을 표시한다. l_counter의 초기값이 0이기 때문에, 루프의 본문에 있는 코드는 종료되기 전에 3번 실행된다.

* __셋째:__ 루프 뒤에 l_counter 값을 표시한다.

## 3. EXIT WHEN statement
EXIT WITH 문에는 다음과 같은 구문이 있다.

```sql
EXIT WHEN condition;
```

EXIT WHIN 문은 WHIN 절의 조건이 TRUE일 때 루프의 현재 반복을 종료한다. 기본적으로 EXIT WHIN 문이 EXIT와 IF THEN 문의 조합이다.

제어가 EXIT WHIN 문에 도달할 때마다 조건이 평가된다. 조건이 TRUE로 평가되면 루프가 종료된다. 그렇지 않으면 EXIT WHIN 절이 아무 동작도 하지 않습니다. 루프 본체 내에서 무한 루프를 방지하려면 특정 시점에서 조건을 TRUE로 설정해야 한다.

다음 예에서는 EXIT WHIN 문을 사용하여 루프를 종료한다.

```sql
DECLARE
    l_counter NUMBER := 0;
BEGIN
    LOOP
        l_counter := l_counter + 1;
        EXIT WHEN l_counter > 3;
    dbms_output.put_line( 'Inside loop: ' || l_counter );
    END LOOP;

    -- control resumes here after EXIT
    dbms_output.put_line( 'After loop: ' || l_counter );
END;
```

이 예제는 EXIT 위 의 명령문을 사용하는 예제와 논리적으로 동일하다.

## 4. Nested loops
다음 예와 같이 LOOP 문을 다른 LOOP 문에 중첩시킬 수 있다.

```sql
DECLARE
    l_i NUMBER := 0;
    l_j NUMBER := 0;
BEGIN
    <<outer_loop>>
    LOOP
        l_i := l_i + 1;
        EXIT outer_loop WHEN l_i > 2;
        dbms_output.put_line('Outer counter ' || l_i);
        -- reset inner counter
        l_j := 0;
            <<inner_loop>> LOOP
            l_j := l_j + 1;
            EXIT inner_loop WHEN l_j > 3;
            dbms_output.put_line(' Inner counter ' || l_j);
        END LOOP inner_loop;
    END LOOP outer_loop;
END;
```

다음은 결과이다.

```text
Outer counter 1
 Inner counter 1
 Inner counter 2
 Inner counter 3
Outer counter 2
 Inner counter 1
 Inner counter 2
 Inner counter 3
```

## [출처 및 참고]
* <https://www.oracletutorial.com/plsql-tutorial/plsql-loop/>
