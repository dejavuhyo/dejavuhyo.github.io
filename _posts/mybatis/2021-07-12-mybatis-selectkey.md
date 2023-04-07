---
title: MyBatis selectKey 사용 방법
author: dejavuhyo
date: 2021-07-12 06:00:00 +0900
categories: [Framework, MyBatis]
tags: [mybatis-selectkey, selectkey, 키값-리턴]
---

## 1. selectKey
키값을 가져와서 증가시켜서 입력하거나 혹은 입력 후에 증가된 키값을 가져올 필요가 있다. MyBatis에서 제공하는 selectKey를 이용하면 별도의 쿼리 로직을 등록할 필요 없이 해당 메소드에서 일괄 처리할 수 있다.

### 1) 기본 문법

```xml
<selectKey keyProperty="memberId" resultType="integer" order="BEFORE">
    SELECT MAX(member_id)+1 FROM member
</selectKey>
```

### 2) 엘리먼트 속성

| 속성 | 설명 |
|:-----:|:-----:|
| keyProperty | selectKey 구문의 결과가 세팅될 대상 프로퍼티 |
| keyColumn | 반환되는 결과 셋의 컬럼 명은 프로퍼티에 일치. 여러 개의 컬럼을 사용한다면 컬럼 명의 목록은 콤마를 사용해서 구분 |
| resultType | 결과의 타입. MyBatis는 이 기능을 제거할 수 있지만 추가하는 것이 문제가 되지 않음. MyBatis는 String을 포함하여 키로 사용될 수 있는 간단한 타입을 허용 |
| order | BEFORE 또는 AFTER를 세팅할 수 있음. BEFORE로 설정하면 키를 먼저 조회하고 그 값을 keyProperty 에 세팅한 뒤 insert 구문을 실행. AFTER로 설정하면 insert 구문을 실행한 뒤 selectKey 구문을 실행. 오라클과 같은 데이터베이스에서는 insert구문 내부에서 일관된 호출 형태로 처리 |
| statementType | MyBatis는 Statement, PreparedStatement 그리고 CallableStatement을 매핑하기 위해 STATEMENT, PREPARED 그리고 CALLABLE 구문 타입을 지원 |

## 2. 예제
반환 값은 parameterType에 넘겨준 객체로 넘어간다. 즉, 이 메소드를 호출한 클래스에서 파라미터로 넘긴 memberVO에서 memberVO.getMemberId()로 해당 값을 가져올 수 있다.

### 1) BEFORE insert
memberId 값을 기존 최댓값에 +1한 다음에 그 값을 입력한다.

```xml
<insert id="insertMember" parameterType="memberVO">
    <selectKey keyProperty="memberId" resultType="integer" order="BEFORE">
        SELECT MAX(member_id)+1 FROM member
    </selectKey>

    INSERT INTO member(member_id, member_name, member_age)
    VALUES (#{memberId}, #{memberName}, #{memberAge})
</insert>
```

### 2) AFTER insert
memberId 입력 후 입력된 값의 최댓값을 가져온다.

```xml
<insert id="insertMember" parameterType="memberVO">
    INSERT INTO member(member_id, member_name, member_age)
    VALUES (#{memberId}, #{memberName}, #{memberAge})

    <selectKey keyProperty="memberId" resultType="integer" order="AFTER">
        SELECT MAX(member_id) FROM member
    </selectKey>
</insert>
```

> **주의:** selectKey를 통해서 나오는 데이터의 row 수는 무조건 1개여야 한다.

## [출처 및 참고]
* [https://yookeun.github.io/java/2014/07/11/mybatis-selectkey/](https://yookeun.github.io/java/2014/07/11/mybatis-selectkey/)
* [https://mybatis.org/mybatis-3/ko/sqlmap-xml.html](https://mybatis.org/mybatis-3/ko/sqlmap-xml.html)
