---
title: PL/SQL FOR LOOP
author: dejavuhyo
date: 2021-04-15 06:10:00 +0900
categories: [Database, Oracle]
tags: [for-loop, plsql-for-loop, for-루프, plsql-for-루프]
---

## 1. Introduction
PL/SQL FOR LOOP는 지정된 횟수만큼 일련의 문을 실행합니다. PL/SQL FOR LOOP 문의 구조는 다음과 같다.

```sql
FOR index IN lower_bound .. upper_bound
LOOP
    statements;
END LOOP;
```

인덱스는 암시적 변수이다. FOR LOOP 문의 로컬입니다. 즉, 루프 외부에서 참조할 수 없다.

루프 내에서 인덱스를 참조할 수는 있지만 값을 변경할 수는 없다. FOR LOOP 문이 실행된 후 인덱스가 정의되지 않는다.

lower_bound와 upper_bound 모두 숫자로 평가하는 숫자 또는 식이다. FOR LOOP 문이 시작되면 lower_bound 및 upper_bound가 한 번 평가된다. 해당 값은 임시 PLS_INTER 값으로 저장된다. lower_bound 및 upper_bound의 결과는 필요한 경우 가장 가까운 정수로 반올림된다.

루프 내에서 lower_bound 또는 upper_bound의 값을 수정하면 첫 번째 루프 반복이 시작되기 전에 한 번만 계산되므로 변경 내용은 영향을 주지 않는다.

일반적으로 lower_bound는 upper_bound보다 작다. 이 경우 인덱스가 lower_bound로 설정되고 문이 실행되며 control이 루프의 상단으로 반환되며, 여기서 인덱스는 upper_bound와 비교된다. 인덱스가 upper_bound보다 작으면 인덱스가 1씩 증가되고 문이 실행되며 컨트롤이 다시 루프 맨 위로 돌아간다. 인덱스가 upper_bound보다 크면 루프가 종료되고 FOR LOOP 문 뒤에 있는 문으로 컨트롤이 전송된다.

lower_bound가 upper_bound와 같으면 문이 한 번만 실행된다. lower_bound가 upper_bound보다 크면 문이 전혀 실행되지 않는다.

## 2. FOR LOOP examples
FOR LOOP 문을 사용하여 작동 방식을 이해하는 예이다.

### 1) 단순 PL/SQL FOR LOOP 예제
예에서 루프 인덱스는 l_counter, lower_bound는 1이고 upper_bound는 5이다. 이 루프에는 1부터 5까지의 정수 목록이 표시된다.

```sql
BEGIN
    FOR l_counter IN 1..5
    LOOP
        DBMS_OUTPUT.PUT_LINE( l_counter );
    END LOOP;
END;
```

결과는 다음과 같다.

```text
1
2
3
4
5
```

### 2) FOR LOOP 문의 STEP 절 시뮬레이션
루프 인덱스는 각 루프 반복 후 1씩 증가하며, 예를 들어 2, 3, 4로 증분을 변경할 수 없다. 그러나 예제와 같이 추가 변수를 사용하여 2, 3, 4 등의 증분을 시뮬레이션할 수 있다.

```sql
DECLARE
    l_step PLS_INTEGER := 2;
BEGIN
    FOR l_counter IN 1..5 LOOP
        dbms_output.put_line (l_counter * l_step);
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

결과는 각 루프 반복 후 출력 번호가 1이 아니라 2가 증가한다는 것을 보여준다.

### 3) 루프 인덱스와 이름이 같은 참조 변수
다음 예를 참고한다.

```sql
DECLARE
    l_counter PLS_INTEGER := 10;
BEGIN
    FOR l_counter IN 1..5 loop
        DBMS_OUTPUT.PUT_LINE (l_counter);
    end loop;
    -- after the loop
    DBMS_OUTPUT.PUT_LINE (l_counter);
END;
```

결과는 다음과 같다.

```text
1
2
3
4
5
10
```

이 예에서는 l_counter라는 변수가 있었는데, 이 변수는 인덱스의 이름이기도 한다. 결과는 FOR 루프에 있는 l_counter가 인클로저 블록에 선언된 변수 l_counter를 숨긴다는 것을 보여준다.

루프 내부의 변수 l_counter를 참조하려면 아래와 같이 블록 레이블을 사용하여 조건을 지정해야 한다.

```sql
<<outer>>
DECLARE
    l_counter PLS_INTEGER := 10;
BEGIN
    FOR l_counter IN 1..5 loop
        DBMS_OUTPUT.PUT_LINE ('Local counter:' ||  l_counter);
        outer.l_counter := l_counter;
    end loop;
    -- after the loop
    DBMS_OUTPUT.PUT_LINE ('Global counter' || l_counter);
END outer;
```

### 4) FOR LOOP 외부에 있는 루프 인덱스 참조
다음 예제는 정의되지 않은 루프 인덱스가 FOR LOOP 문 밖에 있기 때문에 오류를 발생시킨다.

```sql
BEGIN
    FOR l_index IN 1..3 loop
        DBMS_OUTPUT.PUT_LINE (l_index);
    END LOOP;
    -- referencing index after the loop
    DBMS_OUTPUT.PUT_LINE (l_index);
END;
```

Oracle에서 다음 오류를 발생시켰다.

```text
PLS-00201: identifier 'L_INDEX' must be declared
```

## 3. FOR LOOP with REVERSE keyword
다음은 REVERSE 키워드가 있는 FOR LOOP 문의 구조를 보여준다.

REVERSE 키워드를 사용하면 인덱스가 upper_bound로 설정되고 lower_bound에 도달할 때까지 각 루프 반복에서 1씩 감소한다.

```sql
BEGIN
    FOR l_counter IN REVERSE 1..3
    LOOP
        DBMS_OUTPUT.PUT_LINE( l_counter );
    END LOOP;
END;
```

결과는 다음과 같다.

```text
3
2
1
```

REVERSE 키워드가 없으면 결과는 다음과 같다.

```text
1
2
3
```

## [출처 및 참고]
* <https://www.oracletutorial.com/plsql-tutorial/plsql-for-loop/>
