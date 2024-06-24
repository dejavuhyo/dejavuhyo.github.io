---
title: JPA 기본 Column 값
author: dejavuhyo
date: 2024-06-24 22:09:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa, jpa-column, default-value, 컬럼-기본값]
---

## 1. Entity를 생성하는 동안
기본 열 값을 설정하는 첫 번째 방법은 이를 엔터티 속성 값으로 직접 설정하는 것이다.

```java
@Entity
public class User {
    @Id
    private Long id;
    private String firstName = "John Snow";
    private Integer age = 25;
    private Boolean locked = false;
}
```

이제 new 연산자를 사용하여 엔터티를 생성할 때마다 기본값이 설정된다.

```java
@Test
void saveUser_shouldSaveWithDefaultFieldValues() {
    User user = new User();
    user = userRepository.save(user);
    
    assertEquals(user.getName(), "John Snow");
    assertEquals(user.getAge(), 25);
    assertFalse(user.getLocked());
}
```

이 솔루션에는 한 가지 단점이 있다.

SQL 테이블 정의를 살펴보면 기본값이 표시되지 않다.

```sql
create table user
(
    id     bigint not null constraint user_pkey primary key,
    name   varchar(255),
    age    integer,
    locked boolean
);
```

따라서 이를 null로 재정의하면 엔터티는 오류 없이 저장된다.

```java
@Test
void saveUser_shouldSaveWithNullName() {
    User user = new User();
    user.setName(null);
    user.setAge(null);
    user.setLocked(null);
    user = userRepository.save(user);

    assertNull(user.getName());
    assertNull(user.getAge());
    assertNull(user.getLocked());
}
```

## 2. 스키마 정의에서
SQL 테이블 정의에서 직접 기본값을 생성하려면 `@Column` 주석을 사용 하고 해당 columnDefinition 매개변수를 설정할 수 있다.

```java
@Entity
public class User {
    @Id
    Long id;

    @Column(columnDefinition = "varchar(255) default 'John Snow'")
    private String name;

    @Column(columnDefinition = "integer default 25")
    private Integer age;

    @Column(columnDefinition = "boolean default false")
    private Boolean locked;
}
```

이 방법을 사용하면 SQL 테이블 정의에 기본값이 표시된다.

```sql
create table user
(
    id     bigint not null constraint user_pkey primary key,
    name   varchar(255) default 'John Snow',
    age    integer      default 35,
    locked boolean      default false
);
```

그리고 엔터티는 기본값으로 올바르게 저장된다.

```java
@Test
void saveUser_shouldSaveWithDefaultSqlValues() {
    User user = new User();
    user = userRepository.save(user);

    assertEquals(user.getName(), "John Snow");
    assertEquals(user.getAge(), 25);
    assertFalse(user.getLocked());
}
```

이 솔루션을 사용하면 엔터티를 처음 저장할 때 지정된 열을 null로 설정할 수 없다. 값을 제공하지 않으면 기본 값이 자동으로 설정된다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-default-column-values](https://www.baeldung.com/jpa-default-column-values)
