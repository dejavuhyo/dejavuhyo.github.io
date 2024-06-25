---
title: JPA @Basic 주석
author: dejavuhyo
date: 2024-06-25 22:18:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa, jpa-basic, basic-annotation, basic-주석]
---

## 1. 기본 유형
JPA는 기본 유형이라고도 하는 엔터티의 지속 가능한 필드로 다양한 Java 데이터 유형을 지원한다.

기본 유형은 데이터베이스의 열에 직접 매핑된다. 여기에는 Java 기본 요소 및 해당 래퍼 클래스, String, `java.math.BigInteger` 및 `java.math.BigDecimal`, 사용 가능한 다양한 date-time 클래스, 열거형 및 `java.io.Serialized`를 구현하는 기타 유형이 포함된다.

다른 ORM 벤더와 마찬가지로 Hibernate는 기본 유형의 레지스트리를 유지하고 이를 사용하여 열의 특정 `org.hibernate.type.Type`을 해결한다.

## 2. @Basic 주석
`@Basic` 주석을 사용하여 기본 유형 속성을 표시할 수 있다.

```java
@Entity
public class Course {

    @Basic
    @Id
    private int id;

    @Basic
    private String name;
    ...
}
```

즉, 필드나 속성의 `@Basic` 주석은 그것이 기본 유형이고 Hibernate가 지속성을 위해 표준 매핑을 사용해야 함을 나타낸다.

선택적 주석이라는 점에 유의한다. 따라서 Course 엔터티를 다음과 같이 다시 작성할 수 있다.

```java
@Entity
public class Course {

    @Id
    private int id;

    private String name;
    ...
}
```

기본 유형 속성에 `@Basic` 주석을 지정하지 않으면 암시적으로 가정되며 이 주석의 기본값이 적용된다.

## 3. @Basic 주석 사용 이유
`@Basic` 주석에는 optional과 fetch라는 두 가지 속성이 있다.

optional 속성은 표시된 필드 또는 속성이 null을 허용하는지 여부를 정의하는 boolean 매개변수이다. 기본값은 true 이다. 따라서 필드가 기본 유형이 아닌 경우 기본 열은 기본적으로 null 허용 하는 것으로 간주된다.

fetch 속성은 표시된 필드나 속성을 느리게 로드해야 하는지 아니면 즉시 가져와야 하는지 여부를 지정하는 Fetch 열거 형의 멤버를 허용한다. 기본값은 `FetchType.EAGER` 이지만 `FetchType.LAZY`로 설정하여 지연 로딩을 허용할 수 있다.

지연 로딩은 기본 유형으로 매핑된 대규모 직렬화 가능 객체가 있는 경우에만 의미가 있다. 이 경우 필드 액세스 비용이 상당할 수 있다.

Course 이름에 null을 허용 하지 않고 해당 속성도 지연 로드한다고 가정한다. 그런 다음 Course 엔터티를 다음과 같이 정의한다.

```java
@Entity
public class Course {
    
    @Id
    private int id;
    
    @Basic(optional = false, fetch = FetchType.LAZY)
    private String name;
    ...
}
```

optional 및 fetch 매개변수의 기본값에서 벗어나려는 경우 `@Basic` 주석을 명시적으로 사용해야 한다. 필요에 따라 이러한 속성 중 하나 또는 둘 다를 지정할 수 있다.

## 4. JPA @Basic vs @Column
`@Basic` 주석과 `@Column` 주석의 차이점이다.

* `@Basic` 주석의 속성은 JPA 엔터티에 적용되는 반면 `@Column`의 속성은 데이터베이스 열에 적용된다.

* `@Basic` 주석의 선택적 속성은 엔터티 필드가 null 일 수 있는지 여부를 정의한다. 반면에 `@Column` 주석의 nullable 속성은 해당 데이터베이스 열이 null 일 수 있는지 여부를 지정한다.

* `@Basic`을 사용하여 필드가 느리게 로드되어야 함을 나타낼 수 있다.

* `@Column` 주석을 사용하면 매핑된 데이터베이스 열의 이름을 지정할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-basic-annotation](https://www.baeldung.com/jpa-basic-annotation)
