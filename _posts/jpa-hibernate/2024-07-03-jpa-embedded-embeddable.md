---
title: JPA @Embedded 및 @Embeddable
author: dejavuhyo
date: 2024-07-03 10:53:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-embedded, jpa-embeddable, embedded-embeddable]
---

## 1. Data Model Context
Company라는 테이블을 정의한다.

회사 테이블에는 회사 이름, 주소, 전화번호와 같은 기본 정보와 연락처 정보가 저장된다.

```java
public class Company {

    private Integer id;

    private String name;

    private String address;

    private String phone;

    private String contactFirstName;

    private String contactLastName;

    private String contactPhone;

    // standard getters, setters
}
```

그러나 연락 담당자는 별도의 클래스로 추상화되어야 한다.

문제는 그 세부 정보에 대해 별도의 표를 만들고 싶지 않다는 것이다.

## 2. @Embeddable
JPA는 `@Embeddable` 주석을 제공하여 클래스가 다른 엔터티에 의해 내장될 것임을 선언한다.

연락처 세부 정보를 추상화하기 위한 클래스를 정의한다.

```java
@Embeddable
public class ContactPerson {

    private String firstName;

    private String lastName;

    private String phone;

    // standard getters, setters
}
```

## 3. @Embedded
JPA 주석 `@Embedded`는 유형을 다른 엔터티에 내장하는데 사용된다.

Company 클래스를 수정한다.

JPA 주석을 추가하고 별도의 필드 대신 ContactPerson을 사용하도록 변경한다.

```java
@Entity
public class Company {

    @Id
    @GeneratedValue
    private Integer id;

    private String name;

    private String address;

    private String phone;

    @Embedded
    private ContactPerson contactPerson;

    // standard getters, setters
}
```

그 결과, 연락처 정보를 내장하고 단일 데이터베이스 테이블에 매핑하는 엔티티 Company가 탄생했다.

하지만 아직 하나의 문제가 남았는데, 그것은 JPA가 이러한 필드를 데이터베이스 열에 매핑하는 방법이라는 것이다.

## 4. 속성 재정의
필드는 원래 Company 클래스에서 contactFirstName과 같은 것으로 불렸고, 지금은 ContactPerson 클래스에서 firstName으로 불렸다. 따라서 JPA는 이를 각각 contact_first_name과 first_name에 매핑하고 싶어할 것이다.

이상적이지 않다는 점 외에도, 중복된 전화 번호란으로 인해 실제로 문제가 생길 것이다.

따라서 `@AttributeOverrides`와 `@AttributeOverride`를 사용하여 내장된 유형의 열 속성을 재정의할 수 있다.

Company 엔터티의 ContactPerson 필드에 이것을 추가한다.

```java
@Embedded
@AttributeOverrides({
  @AttributeOverride( name = "firstName", column = @Column(name = "contact_first_name")),
  @AttributeOverride( name = "lastName", column = @Column(name = "contact_last_name")),
  @AttributeOverride( name = "phone", column = @Column(name = "contact_phone"))
})
private ContactPerson contactPerson;
```

이러한 주석이 필드에 추가되므로 각 둘러싼 엔터티에 대해 다른 재정의를 가질 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-embedded-embeddable](https://www.baeldung.com/jpa-embedded-embeddable)
