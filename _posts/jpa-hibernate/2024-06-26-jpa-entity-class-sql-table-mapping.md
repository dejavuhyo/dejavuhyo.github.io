---
title: JPA 엔터티 클래스 SQL 테이블 매핑
author: dejavuhyo
date: 2024-06-26 17:22:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa, jpa-mapping, entity-table-mapping, entity-table, 엔터티-테이블-매핑]
---

## 1. 기본 테이블 이름
JPA 기본 테이블 이름 생성은 구현에 따라 다르다.

예를 들어, Hibernate에서 기본 테이블 이름은 첫 글자가 대문자인 클래스 이름이다. 이는 ImplicitNamingStrategy 계약을 통해 결정된다.

그러나 PhysicalNamingStrategy 인터페이스를 구현 하여 이 동작을 변경할 수 있다.

## 2. @Table 사용
사용자 정의 SQL 테이블 이름을 설정하는 가장 쉬운 방법은 엔터티에 `@jakarta.persistence.Table` 주석을 달고 이름 매개변수를 정의하는 것이다.

```java
@Entity
@Table(name = "ARTICLES")
public class Article {
    // ...
}
```

정적 최종 변수에 테이블 이름을 저장할 수도 있다.

```java
@Entity
@Table(name = Article.TABLE_NAME)
public class Article {
    public static final String TABLE_NAME= "ARTICLES";
    // ...
}
```

## 3. JPQL 쿼리에서 테이블 덮어쓰기
기본적으로 JPQL 쿼리에서는 엔터티 클래스 이름을 사용한다.

```sql
select * from Article
```

하지만 `@jakarta.persistence.Entity` 주석에 name 매개변수를 정의하여 이를 변경할 수 있다.

```java
@Entity(name = "MyArticle")
```

그런 다음 JPQL 쿼리를 다음과 같이 변경한다.

```sql
select * from MyArticle
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-entity-table-names](https://www.baeldung.com/jpa-entity-table-names)
