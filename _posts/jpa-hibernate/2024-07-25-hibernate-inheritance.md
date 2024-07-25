---
title: Hibernate 상속 매핑
author: dejavuhyo
date: 2024-07-25 10:47:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate-inheritance, inheritance-mapping, 하이버네이트-상속, 상속-매핑]
---

## 1. 매핑
관계형 데이터베이스는 클래스 계층을 데이터베이스 테이블에 매핑하는 간단한 방법이 없다.

이를 해결하기 위해 JPA 사양은 여러 가지 전략을 제공한다.

* MappedSuperclass - 부모 클래스, 엔터티가 될 수 없다.

* Single Table - 공통 조상을 가진 서로 다른 클래스의 엔터티가 단일 테이블에 배치된다.

* Joined Table - 각 클래스에는 테이블이 있으며, 하위 클래스 엔터티를 쿼리하려면 테이블을 조인해야 한다.

* 클래스별 테이블 - 클래스의 모든 속성은 해당 테이블에 있으므로 조인이 필요하지 않다.

각 전략은 서로 다른 데이터베이스 구조를 만들어낸다.

엔티티 상속은 슈퍼클래스에 대한 쿼리를 실행할 때 다형성 쿼리를 사용하여 모든 서브클래스 엔티티를 검색할 수 있음을 의미한다.

Hibernate는 JPA 구현이므로 위에서 언급한 모든 기능뿐 아니라 상속과 관련된 몇 가지 Hibernate 특정 기능도 포함한다.

## 2. MappedSuperclass
MappedSuperclass 전략을 사용하면 상속은 클래스에서만 명확해지고 엔터티 모델에서는 명확하지 않다.

부모 클래스를 나타내는 Person 클래스를 만든다.

```java
@MappedSuperclass
public class Person {

    @Id
    private long personId;
    private String name;

    // constructor, getters, setters
}
```

이 클래스에는 더 이상 `@Entity` 주석이 없다. 이 클래스는 그 자체로 데이터베이스에 저장되지 않기 때문이다.

Employee 하위 클래스를 추가한다.

```java
@Entity
public class MyEmployee extends Person {
    private String company;
    // constructor, getters, setters 
}
```

데이터베이스에서 이는 하위 클래스의 선언된 필드와 상속된 필드에 대한 세 개의 열이 있는 하나의 MyEmployee 테이블에 해당한다.

이 전략을 사용하면 조상은 다른 엔터티와의 연결을 포함할 수 없다.

## 3. Single Table
Single Table 전략은 각 클래스 계층에 대해 하나의 테이블을 생성한다. JPA는 명시적으로 지정하지 않으면 기본적으로 이 전략을 선택한다.

슈퍼클래스에 `@Inheritance` 주석을 추가하여 사용하려는 전략을 정의할 수 있다.

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
public class MyProduct {
    @Id
    private long productId;
    private String name;

    // constructor, getters, setters
}
```

엔터티의 식별자도 슈퍼클래스에 정의되어 있다.

그런 다음 하위 클래스 엔터티를 추가할 수 있다.

```java
@Entity
public class Book extends MyProduct {
    private String author;
}
```

```java
@Entity
public class Pen extends MyProduct {
    private String color;
}
```

### 1) 판별자 값
모든 엔터티에 대한 레코드가 동일한 테이블에 있으므로 Hibernate에서는 이를 구별할 방법이 필요하다.

기본적으로 이 작업은 엔터티 이름을 값으로 갖는 DTYPE 이라는 ​​판별자 열을 통해 수행된다.

판별자 열을 사용자 지정하려면 `@DiscriminatorColumn` 주석을 사용할 수 있다.

```java
@Entity(name="products")
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name="product_type", 
  discriminatorType = DiscriminatorType.INTEGER)
public class MyProduct {
    // ...
}
```

여기서 product_type 이라는 정수 열을 기준으로 MyProduct 하위 클래스 엔터티를 구별한다.

product_type 열에 대해 각 하위 클래스 레코드가 어떤 값을 가질지 Hibernate에 알려줘야 한다.

```java
@Entity
@DiscriminatorValue("1")
public class Book extends MyProduct {
    // ...
}
```

```java
@Entity
@DiscriminatorValue("2")
public class Pen extends MyProduct {
    // ...
}
```

Hibernate는 주석이 취할 수 있는 두 가지 다른 미리 정의된 값(null 및 not null)을 추가한다.

* `@DiscriminatorValue("null")` - 판별자 값이 없는 모든 행이 이 주석이 있는 엔터티 클래스에 매핑됨을 의미한다. 이는 계층 구조의 루트 클래스에 적용될 수 있다.

* `@DiscriminatorValue("not null")` – 엔티티 정의와 연관된 어떤 것과도 일치하지 않는 판별자 값을 갖는 모든 행은 이 주석이 있는 클래스에 매핑된다.
열 대신 Hibernate 특정 `@DiscriminatorFormula` 주석을 사용하여 차별화 값을 결정할 수도 있다.

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorFormula("case when author is not null then 1 else 2 end")
public class MyProduct { ... }
```

이 방법은 부모 엔터티를 쿼리할 때 하나의 테이블에만 액세스하면 되므로 다형성 쿼리 성능의 이점이 있다.

반면에 이는 하위 클래스 엔터티 속성에 더 이상 NOT NULL 제약 조건을 사용할 수 없다는 것을 의미한다.

## 4. Joined Table
이 방법을 사용하면 계층 구조의 각 클래스가 해당 테이블에 매핑된다. 모든 테이블에 반복적으로 나타나는 유일한 열은 식별자이며, 필요할 때 조인하는데 사용된다.

이 전략을 사용하는 슈퍼클래스를 만든다.

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public class Animal {
    @Id
    private long animalId;
    private String species;

    // constructor, getters, setters 
}
```

그러면 간단히 하위 클래스를 정의할 수 있다.

```java
@Entity
public class Pet extends Animal {
    private String name;

    // constructor, getters, setters
}
```

두 테이블 모두 animalId 식별자 열이 있다.

Pet 엔터티의 기본 키에는 부모 엔터티의 기본 키에 대한 외래 키 제약 조건도 있다.

이 열을 사용자 지정하려면 `@PrimaryKeyJoinColumn` 주석을 추가할 수 있다.

```java
@Entity
@PrimaryKeyJoinColumn(name = "petId")
public class Pet extends Animal {
    // ...
}
```

이 상속 매핑 방식의 단점은 엔터티를 검색하려면 테이블 간 조인이 필요하므로 레코드 수가 많은 경우 성능이 저하될 수 있다.

부모 클래스를 쿼리할 경우 모든 관련 자식과 조인하므로 조인 수가 더 많다. 따라서 레코드를 검색하려는 계층 구조가 높을수록 성능에 영향을 줄 가능성이 더 크다.

## 5. 클래스별 테이블
클래스별 테이블 전략은 각 엔터티를 해당 테이블에 매핑한다. 테이블에는 상속된 속성을 포함하여 엔터티의 모든 속성이 포함된다.

결과 스키마는 `@MappedSuperclass`를 사용한 스키마와 유사하다. 하지만 Table per Class는 실제로 부모 클래스에 대한 엔티티를 정의하여 결과적으로 연결 및 다형성 쿼리를 허용한다.

이 전략을 사용하려면 기본 클래스에 `@Inheritance` 주석만 추가하면 된다.

```java
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public class Vehicle {
    @Id
    private long vehicleId;

    private String manufacturer;

    // standard constructor, getters, setters
}
```

그런 다음 표준적인 방법으로 하위 클래스를 만들 수 있다.

이는 상속 없이 각 엔터티를 매핑하는 것과 크게 다르지 않다. 기본 클래스를 쿼리할 때 구분이 명확해지며, 백그라운드에서 UNION 문을 사용하여 모든 하위 클래스 레코드도 반환한다.

UNION을 사용하면 이 전략을 선택할 때 성능이 저하될 수도 있다. 또 다른 문제는 더 이상 ID 키 생성을 사용할 수 없다는 것이다.

## 6. 다형성 쿼리
언급한 대로 기본 클래스를 쿼리하면 모든 하위 클래스 엔터티도 검색된다.

JUnit 테스트를 통해 이 동작이 실제로 어떻게 나타나는지 확인한다.

```java
@Test
public void givenSubclasses_whenQuerySingleTableSuperclass_thenOk() {
    Book book = new Book(1, "1984", "George Orwell");
    session.save(book);
    Pen pen = new Pen(2, "my pen", "blue");
    session.save(pen);

    assertThat(session.createQuery("from MyProduct")
      .getResultList()).size()).isEqualTo(2);
}
```

예제에서 두 개의 Book과 Pen 객체를 생성한 다음, 그들의 슈퍼클래스인 MyProduct를 쿼리하여 두 개의 객체를 검색할 수 있는지 확인했다.

Hibernate는 엔티티가 아니지만 엔티티 클래스에 의해 확장되거나 구현되는 인터페이스나 기본 클래스를 쿼리할 수도 있다.

`@MappedSuperclass` 예제를 사용하여 JUnit 테스트를 확인한다.

```java
@Test
public void givenSubclasses_whenQueryMappedSuperclass_thenOk() {
    MyEmployee emp = new MyEmployee(1, "john", "baeldung");
    session.save(emp);

    assertThat(session.createQuery(
      "from com.baeldung.hibernate.pojo.inheritance.Person")
      .getResultList())
      .hasSize(1);
}
```

이것은 `@MappedSuperclass` 이든 아니든 모든 슈퍼클래스나 인터페이스에 적용된다. 일반적인 HQL 쿼리와의 차이점은 Hibernate에서 관리하는 엔티티가 아니기 때문에 완전히 정규화된 이름을 사용해야 한다는 것이다.

이 유형의 쿼리에서 하위 클래스가 반환되는 것을 원하지 않으면 Hibernate `@Polymorphism` 주석을 정의에 EXPLICIT 유형으로 추가하기만 하면 된다.

```java
@Entity
@Polymorphism(type = PolymorphismType.EXPLICIT)
public class Bag implements Item { ...}
```

이 경우, Items를 쿼리할 때 Bag 클래스가 명시적이기 때문에 Item 엔터티를 확인할 수 없다는 예외가 발생한다. 이러한 제한으로 인해 `@Polymorphism(type = PolymorphismType.IMPLICIT)`이 있는 다른 클래스를 정의해야 한다. Items를 쿼리할 때 해당 인터페이스를 구현하는 두 객체에서 레코드를 검색한다.

## [출처 및 참고]
* [https://www.baeldung.com/hibernate-inheritance](https://www.baeldung.com/hibernate-inheritance)
