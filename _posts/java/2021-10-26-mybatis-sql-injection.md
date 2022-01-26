---
title: MyBatis SQL 삽입공격
author: dejavuhyo
date: 2021-10-26 06:00:00 +0900
categories: [Language, Java]
tags: [mybatis-data-map, sql-injection, mybatis-sql-삽입공격, sql-삽입공격]
---

## 1. 정의
외부에서 입력된 값이 질의어의 인자값으로만 사용되지 않고, 질의 명령어에 연결되는 문자열로 사용되면, 공격자가 의도하지 않았던 문자열을 전달함으로써 질의문의 의미를 왜곡시키거나 그 구조를 변경하여 임의의 데이터베이스 명령어를 수행할 수 있다.

## 2. 안전한 코딩 기법

* 외부의 입력으로부터 위험한 문자나 의도하지 않았던 입력을 제거하는 코드를 프로그램 내에 포함한다.

* MyBatis Data Map 파일의 인자를 받는 질의 명령어 정의 시에 문자열 삽입 인자 `${인자이름}`를 사용하지 않는다. 즉 `#{인자이름}` 형태의 질의문을 사용한다.

## 3. 예제
다음의 예제는 MyBatis Data Map에서 사용하는 질의문 설정파일(XML)이다. 정의된 질의문 중 delStudnet 명령어 선언에서 질의문에 삽입되는 인자 중 `$name`으로 전달되는 문자열 값은 그대로 연결되어 질의문이 만들어진다. 따라서 만약 name의 값으로 `' OR 'x'='x'`을 전달하면 다음과 같은 질의문이 수행되어 테이블의 모든 원소를 삭제하게 된다.

```sql
DELETE STUDENTS WHERE NUM = #num and NAME = '' OR 'x'='x'
```

* 안전하지 않은 코드의 예

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMap PUBLIC "-//iBATIS.com//DTD SQL Map 2.0//EN" "http://www.ibatis.com/dtd/sql-map-2.dtd">

<sqlMap namespace="Student">
    <resultMap id="StudentResult" class="Student">
        <result column="ID" property="id" />
        <result column="NAME" property="name" />
    </resultMap>

    <select id="listStudents" resultMap="StudentResult">
        SELECT NUM, NAME
        FROM STUDENTS
        ORDER BY NUM
    </select>

    <select id="nameStudent" parameterClass="Integer" resultClass="Student">
        SELECT NUM, NAME
        FROM STUDENTS
        WHERE NUM = #{num}
    </select>

    <!-- dynamic SQL 사용 -->
    <delete id="delStudent" parameterClass="Student">
        DELETE STUDENTS
        WHERE NUM = #{num} AND NAME = '${name}'
    </delete>
</sqlMap>
```

name 인자를 `#{name}` 형태로 받도록 수정한다.

* 안전한 코드의 예

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMap PUBLIC "-//iBATIS.com//DTD SQL Map 2.0//EN" "http://www.ibatis.com/dtd/sql-map-2.dtd">

<sqlMap namespace="Student">
    <resultMap id="StudentResult" class="Student">
        <result column="ID" property="id" />
        <result column="NAME" property="name" />
    </resultMap>

    <select id="listStudents" resultMap="StudentResult">
        SELECT NUM, NAME
        FROM STUDENTS
        ORDER BY NUM
    </select>

    <select id="nameStudent" parameterClass="Integer" resultClass="Student">
        SELECT NUM, NAME
        FROM STUDENTS
        WHERE NUM = #{num}
    </select>
    
    <!-- static SQL 사용 -->
    <delete id="delStudent" parameterClass="Student">
        DELETE STUDENTS
        WHERE NUM = #{num} AND NAME = #{name}
    </delete>
</sqlMap>
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
