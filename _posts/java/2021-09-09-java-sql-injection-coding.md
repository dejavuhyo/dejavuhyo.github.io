---
title: Java SQL 삽입 코딩 기법
author: dejavuhyo
date: 2021-09-09 06:00:00 +0900
categories: [Language, Java]
tags: [sql-injection, sql-삽입]
---

## 1. 정의
SQL 삽입(Improper Neutralization of Special Elements used in an SQL Command, SQL Injection)은 데이터베이스(DB)와 연동된 웹 어플리케이션에서 입력된 데이터에 대한 유효성 검증을 하지 않을 경우, 공격자가 입력 폼 및 URL 입력란에 SQL 문을 삽입하여 DB로부터 정보를 열람하거나 조작할 수 있는 보안 약점을 말한다.

![sql-injection](/assets/img/2021-09-09-sql-injection/sql-injection.png)

취약한 웹 어플리케이션에서는 사용자로부터 입력된 값을 필터링 과정 없이 넘겨받아 동적 쿼리(Dynamic Query)를 생성한다. 이는 개발자가 의도하지 않은 쿼리가 생성되어 정보 유출에 악용될 수 있다.

## 2. 안전한 코딩 기법

* preparedStatement 클래스와 하위 메소드 executeQuery(), execute(), executeUpdate()를 사용하는 것이 바람직하다.

* preparedStatement 클래스를 사용할 수 없는 환경이라면, 입력값을 필터링 처리한 후 사용한다. 필터링 기준은 SQL 구문 제한, 특수문자 제한, 길이 제한을 복합적으로 사용한다.

## 3. 예제
다음은 안전하지 않은 코드의 예를 나타낸 것으로, 외부로부터 tableName과 name의 값을 받아서 SQL 쿼리를 생성하고 있으며, name의 값으로 `name' OR 'a'='a`를 입력하면 조작된 쿼리를 생성하는 문자열 전달이 가능하다.

* 안전하지 않은 코드의 예

```java
try {
    String tableName = props.getProperty("jdbc.tableName");
    String name = props.getProperty("jdbc.name");
    String query = "SELECT * FROM " + tableName + " WHERE Name =" + name;
    stmt = con.prepareStatement(query);
    rs = stmt.executeQuery();
} catch (SQLException sqle) {
} finally {
}
```

이를 안전한 코드로 변환하면 다음과 같다. 외부로부터 인자를 받는 preparedStatement 객체를 상수 스트링으로 생성하고, 인자 부분을 setXXX 메소드로 설정하여, 외부의 입력이 쿼리문의 구조를 바꾸는 것을 방지하는 것이 필요하다.

* 안전한 코드의 예

```java
try {
    String tableName = props.getProperty("jdbc.tableName");
    String name = props.getProperty("jdbc.name");
    String query = "SELECT * FROM ? WHERE Name = ? ";
    stmt = con.prepareStatement(query);
    stmt.setString(1, tableName);
    stmt.setString(2, name);
    rs = stmt.executeQuery();
} catch (SQLException sqle) {
} finally {
}
```

## [출처 및 참고]
* [https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=](https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=)
* [www.devkuma.com/books/pages/1153](www.devkuma.com/books/pages/1153)
