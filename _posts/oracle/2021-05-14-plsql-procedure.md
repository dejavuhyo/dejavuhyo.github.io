---
title: PL/SQL Procedure
author: dejavuhyo
date: 2021-05-14 09:30:00 +0900
categories: [Database, Oracle]
tags: [procedure, plsql-procedure, 프로시저, plsql-프로시저]
---

## 1. PL/SQL 프로시저 구문
PL/SQL 프로시저는 응용프로그램의 특정 비즈니스 로직을 캡슐화하는 재사용 가능한 단위이다. 기술적으로 PL/SQL 프로시저는 Oracle 데이터베이스에 스키마 개체로 저장된 명명된 블록이다.

다음은 PL/SQL에서 프로시저를 만드는 기본 구문을 보여준다.

```sql
CREATE [OR REPLACE ] PROCEDURE procedure_name (parameter_list)
IS
[declaration statements]

BEGIN
[execution statements]

EXCEPTION
[exception handler]

END
[procedure_name ];
```

## 2. 프로시저 헤더
프로시저는 이름 및 선택적 매개 변수 목록을 지정하는 헤더로 시작한다.

각 파라미터는 IN, OUT 또는 INOUT 모드일 수 있다. 매개 변수 모드는 매개 변수를 읽거나 쓸 수 있는지 여부를 지정한다.

### 1) IN
IN 매개 변수는 읽기 전용이다. 프로시저 내에서 IN 매개 변수를 참조할 수는 있지만 값을 변경할 수는 없다. Oracle은 IN을 기본 모드로 사용한다. 즉, 매개 변수의 모드를 명시적으로 지정하지 않으면 Oracle이 IN 모드를 사용한다.

### 2) OUT
OUT 매개 변수는 쓰기 가능하다. 일반적으로 OUT 매개 변수에 대해 반환된 값을 설정하고 호출 프로그램으로 반환한다. 프로시저는 OUT 매개 변수에 대해 제공하는 값을 무시한다.

### 3) INOUT
INOUT 매개 변수는 읽기 및 쓰기 가능하다. 프로시저를 읽고 수정할 수 있다.

OR REPLACE 옵션을 사용하면 현재 프로시저를 새 코드로 덮어쓸 수 있다.

## 3. 프로시저 바디
익명 블록과 유사하게 프로시저 바디는 세 부분으로 구성된다. 실행 가능한 부분은 필수 사항이고 선언적 부분과 예외 처리 부분은 선택 사항이다. 실행 가능한 부분에는 실행 가능한 문이 하나 이상 포함되어야 한다.

### 1) 선언 부분
이 부분에서는 변수, 상수, 커서 등을 선언할 수 있다. 익명 블록과 달리 프로시저의 선언 부분은 DECLARE 키워드로 시작하지 않는다.

### 2) 실행 가능한 부분
이 부분에는 특정 비즈니스 논리를 구현하는 하나 이상의 문장이 포함되어 있다. NULL 문만 포함할 수 있다.

### 3) 예외 처리 부분
이 부분에는 예외를 처리하는 코드가 포함되어 있다.

## 4. 프로시저 생성 예제
다음 프로시저는 고객 ID를 수락하고 이름, 성, 이메일을 포함한 고객의 연락처 정보를 출력한다.

```sql
CREATE OR REPLACE PROCEDURE print_contact (
    in_customer_id NUMBER
)
IS
    r_contact contacts%ROWTYPE;
BEGIN
    -- get contact based on customer id
    SELECT *
    INTO r_contact
    FROM contacts
    WHERE customer_id = p_customer_id;

    -- print out contact's information
    dbms_output.put_line( r_contact.first_name || ' ' ||
    r_contact.last_name || '<' || r_contact.email ||'>' );

EXCEPTION
    WHEN OTHERS THEN
        dbms_output.put_line( SQLERRM );
END;
```

프로시저를 컴파일하려면 다음 그림과 같이 Run Statement 버튼을 클릭한다.

![procedure-compile](/assets/img/2021-05-14-plsql-procedure/procedure-compile.png)

프로시저가 성공적으로 컴파일되면 아래와 같이 프로시저 노드에서 새 프로시저를 찾을 수 있다.

![procedure-example](/assets/img/2021-05-14-plsql-procedure/procedure-example.png)

## 5. 프로시저 실행
다음은 프로시저를 실행하기 위한 구문을 보여 준다.

```sql
EXECUTE procedure_name( arguments);
```

OR

```sql
EXEC procedure_name( arguments);
```

예를 들어 고객 ID 100의 연락처 정보를 인쇄하는 print_contact 프로시저를 실행하려면 다음 문을 사용한다.

```sql
EXEC print_contact(100);
```

다음은 결과이다.

```text
Elisha Lloyd<elisha.lloyd@verizon.com>
```

다음 단계를 사용하여 Oracle SQL Developer에서 프로시저를 실행할 수도 있다.

* 프로시저 이름을 마우스 오른쪽 버튼으로 클릭하고 Run... 메뉴 항목을 선택한다.

![procedure-execute-1](/assets/img/2021-05-14-plsql-procedure/procedure-execute-1.png)

* in_customer_id 매개 변수의 값을 입력하고 OK 버튼을 클릭한다.

![procedure-execute-2](/assets/img/2021-05-14-plsql-procedure/procedure-execute-2.png)

* 다음은 결과를 보여준다.

```text
Connecting to the database Local.
Elisha Lloyd<elisha.lloyd@verizon.com>
Process exited.
Disconnecting from the database Local.
```

## 6. 프로시저 편집
기존 프로시저의 코드를 변경하려면 다음 단계를 수행한다.

* __1단계:__ Procedures 노드 아래에서 프로시저 이름을 클릭한다.

* __2단계:__ 프로시저 코드를 편집합니다.

* __3단계:__ 컴파일 메뉴 옵션을 클릭하여 프로시저를 다시 컴파일한다.

![procedure-editing-and-compiling](/assets/img/2021-05-14-plsql-procedure/procedure-editing-and-compiling.png)

## 7. 프로시저 제거
프로시저를 삭제하려면, DROP PROCEDURE와 다음 구문에 표시된 대로 삭제할 프로시저의 이름을 사용한다.

```sql
DROP PROCEDURE procedure_name;
```

예를 들어, 다음 명령문은 print_contact 프로시저를 삭제한다.

```sql
DROP PROCEDURE print_contact;
```

다음은 SQL Developer를 사용하여 프로시저를 삭제하는 단계를 보여준다.

* __1단계:__ 삭제할 프로시저 이름을 마우스 오른쪽 버튼으로 누른다.

* __2단계:__ 메뉴 옵션 Drop… 선택한다.

* __3단계:__ 프롬프트 대화 상자에서 Apply 버튼을 클릭하여 프로시저를 제거한다.

![procedure-drop](/assets/img/2021-05-14-plsql-procedure/procedure-drop.png)

![procedure-drop-confirmation](/assets/img/2021-05-14-plsql-procedure/procedure-drop-confirmation.png)

## [출처 및 참고]
* <https://www.oracletutorial.com/plsql-tutorial/plsql-procedure/>