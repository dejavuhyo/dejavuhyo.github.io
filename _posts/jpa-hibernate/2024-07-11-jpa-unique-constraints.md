---
title: JPA 고유 제약
author: dejavuhyo
date: 2024-07-11 10:20:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-unique-constraints, unique-constraints, 고유-제약]
---

## 1. 고유 제약
고유 키는 데이터베이스 테이블의 레코드를 고유하게 식별하는 테이블의 단일 또는 다중 열 집합이다.

고유 키 제약 조건과 기본 키 제약 조건은 모두 열 또는 열 집합의 고유성을 보장한다.

### 1) 기본 키 제약 조건과의 차이점
고유 제약 조건은 열 또는 열 조합의 데이터가 각 행에 대해 고유하도록 보장한다. 예를 들어, 테이블의 기본 키는 암묵적 고유 제약 조건으로 기능하다. 따라서 기본 키 제약 조건은 자동으로 고유 제약 조건을 갖는다.

테이블당 기본 키 제약 조건은 하나만 있을 수 있다. 그러나 테이블당 여러 개의 고유 제약 조건이 있을 수 있다.

간단히 말해서, 고유 제약 조건은 기본 키 매핑에 수반되는 모든 제약 조건에 추가로 적용된다.

정의한 고유 제약 조건은 테이블 생성 중에 적절한 데이터베이스 제약 조건을 생성하는데 사용되며 런타임에 insert, update 또는 delete 명령문을 정렬하는데 사용될 수도 있다.

### 2) 단일 열 및 다중 열 제약 조건
고유 제약 조건은 열 제약 조건 또는 테이블 제약 조건일 수 있다. 테이블 수준에서 여러 열에 걸쳐 고유 제약 조건을 정의할 수 있다.

JPA를 사용하면 `@Column(unique=true)` 및 `@UniqueConstraint`를 사용하여 코드에서 고유한 제약 조건을 정의할 수 있다. 이러한 주석은 스키마 생성 프로세스에 의해 해석되어 자동으로 제약 조건을 생성한다.

무엇보다도, 열 수준 제약 조건은 단일 열에 적용되고 테이블 수준 제약 조건은 전체 테이블에 적용된다.

## 2. Entity 셋업
JPA의 엔티티는 데이터베이스에 저장된 테이블을 나타낸다. 엔티티의 모든 인스턴스는 테이블의 행을 나타낸다.

도메인 엔터티를 만들고 데이터베이스 테이블에 매핑하고 Person 엔터티를 만든다.

```java
@Entity
@Table
public class Person implements Serializable {
    @Id
    @GeneratedValue
    private Long id;  
    private String name;
    private String password;
    private String email;
    private Long personNumber;
    private Boolean isActive;
    private String securityNumber;
    private String departmentCode;
    @JoinColumn(name = "addressId", referencedColumnName = "id")
    private Address address;
   //getters and setters
 }
```

주소 필드는 주소 엔터티의 참조 필드이다.

```java
@Entity
@Table
public class Address implements Serializable {
    @Id
    @GeneratedValue
    private Long id;
    private String streetAddress;
    //getters and setters
}
```

전체에서 이 Person 엔터티를 사용하여 예시를 보여준다.

## 3. 열 제약 조건
모델이 준비되면 첫 번째 고유한 제약 조건을 구현할 수 있다.

사람의 정보를 보관하는 Person 엔터티를 고려한다. id 열에 대한 기본 키가 있다. 이 엔터티는 중복 값이 ​​없는 PersonNumber도 보관한다. 게다가 테이블에 이미 기본 키가 있기 때문에 기본 키를 정의할 수 없다.

이 경우, 열 고유 제약 조건을 사용하여 PersonNumber 필드에 중복 값이 ​​입력되지 않도록 할 수 있다. JPA는 고유 속성이 있는 `@Column` 주석을 사용하여 이를 달성할 수 있도록 한다.

### 1) @Column(unique=true)
주석 유형 Column은 영구 속성이나 필드에 대한 매핑된 열을 지정하는데 사용된다.

```java
@Target(value={METHOD,FIELD})
@Retention(value=RUNTIME)
public @interface Column {
    boolean unique;
   //other elements
}
```

unique 속성은 열이 고유 키인지 여부를 지정한다. 이는 UniqueConstraint 주석의 단축형이며, 고유 키 제약 조건이 단일 열에만 해당할 때 유용하다.

### 2) 열 제약 조건 정의
고유 제약 조건이 하나의 필드에만 기반하는 경우 해당 열에 `@Column(unique=true)`를 사용할 수 있다.

personNumber 필드에 고유한 제약 조건을 정의한다.

```java
@Column(unique=true)
private Long personNumber;
```

스키마 생성 프로세스를 실행하면 로그에서 이를 검증할 수 있다.

```text
[main] DEBUG org.hibernate.SQL - alter table Person add constraint UK_d44q5lfa9xx370jv2k7tsgsqt unique (personNumber)
```

마찬가지로, 사람이 고유한 이메일로 등록하도록 제한하려면 이메일 필드에 고유한 제약 조건을 추가할 수 있다.

```java
@Column(unique=true)
private String email;
```

스키마 생성 프로세스를 실행하고 제약 조건을 확인한다.

```text
[main] DEBUG org.hibernate.SQL - alter table Person add constraint UK_585qcyc8qh7bg1fwgm1pj4fus unique (email)
```

단일 열에 고유 제약 조건을 적용하고자 할 때 유용하지만, 때로는 열의 조합인 복합 키에 고유 제약 조건을 추가하고자 할 수 있다. 복합 고유 키를 정의하려면 테이블 제약 조건을 사용할 수 있다.

## 4. 테이블 제약 조건
복합 고유 키는 열의 조합으로 구성된 고유 키이다. 복합 고유 키를 정의하려면 열 대신 테이블에 제약 조건을 추가할 수 있다. JPA는 `@UniqueConstraint` 주석을 사용하여 이를 달성하는데 도움이 된다.

### 1) @UniqueConstraint 주석
주석 유형 UniqueConstraint는 테이블에 대한 생성된 DDL(데이터 정의 언어)에 고유 제약 조건이 포함되어야 함을 지정한다.

정의이다.

```java
@Target(value={})
@Retention(value=RUNTIME)
public @interface UniqueConstraint {
    String name() default "";
    String[] columnNames();
}
```

각각 `String` 및 `String[]` 유형의 name 및 columnNames는 UniqueConstraint 주석에 대해 지정할 수 있는 주석 요소이다.

### 2) 고유 제약 조건 정의
Person 엔터티를 고려한다. Person은 active 상태에 대한 중복 레코드가 없어야 한다. 즉, personNumber와 isActive를 구성하는 키에 대한 중복 값이 ​​없어야 한다. 여기서 여러 열에 걸쳐 있는 고유한 제약 조건을 추가해야 한다.

JPA는 `@UniqueConstraint` 주석으로 이를 달성하는데 도움이 된다. `@Table` 주석에서 uniqueConstraints 속성 아래에 이를 사용한다.

```java
@Table(uniqueConstraints = { @UniqueConstraint(columnNames = { "personNumber", "isActive" }) })
```

스키마가 생성되면 이를 검증할 수 있다.

```text
[main] DEBUG org.hibernate.SQL - alter table Person add constraint UK5e0bv5arhh7jjhsls27bmqp4a unique (personNumber, isActive)
```

여기서 주의해야 할 점 하나는 이름을 지정하지 않으면 공급자가 생성한 값이라는 것이다. JPA 2.0부터 고유 제약 조건에 이름을 지정할 수 있다.

```java
@Table(uniqueConstraints = { @UniqueConstraint(name = "UniqueNumberAndStatus", columnNames = { "personNumber", "isActive" }) })
```

그리고 동일한 것을 검증할 수 있다.

```text
[main] DEBUG org.hibernate.SQL - alter table Person add constraint UniqueNumberAndStatus unique (personNumber, isActive)
```

여기서 열 집합에 대한 고유 제약 조건을 추가했다. 또한 여러 개의 고유 제약 조건을 추가할 수 있다. 즉, 여러 열 집합에 대한 고유 제약 조건을 추가할 수 있다.

### 3) 단일 엔터티에 대한 여러 고유 제약 조건
테이블에는 여러 개의 고유 제약 조건이 있을 수 있다. 복합 키인 personNumber와 isActive status에 대한 고유 제약 조건을 정의했다. securityNumber와 departmentCode의 조합에 대한 제약 조건을 추가한다.

고유 인덱스를 수집하여 한 번에 지정한다. `@UniqueConstraint` 주석을 중괄호로 반복하고 쉼표로 구분하여 이를 수행한다.

```java
@Table(uniqueConstraints = {
   @UniqueConstraint(name = "UniqueNumberAndStatus", columnNames = {"personNumber", "isActive"}),
   @UniqueConstraint(name = "UniqueSecurityAndDepartment", columnNames = {"securityNumber", "departmentCode"})})
```

로그를 보고 제약 조건을 확인한다.

```text
[main] DEBUG org.hibernate.SQL - alter table Person add constraint UniqueNumberAndStatus unique (personNumber, isActive)
[main] DEBUG org.hibernate.SQL - alter table Person add constraint UniqueSecurityAndDepartment unique (securityNumber, departmentCode)
```

지금까지 같은 엔터티의 필드에 대한 고유한 제약 조건을 정의했다. 그러나 어떤 경우에는 다른 엔터티의 필드를 참조했을 수 있으며 해당 필드의 고유성을 보장해야 할 수도 있다.

## 5. 참조된 테이블 열에 대한 고유 제약 조건
서로 관련된 두 개 이상의 테이블을 만들 때, 종종 한 테이블의 열이 다른 테이블의 기본 키를 참조함으로써 관련된다. 이 열을 "외래 키"라고 한다. 예를 들어, Person 및 Address 엔터티는 addressId 필드를 통해 연결된다. 따라서 addressId는 참조 테이블 열 역할을 한다.

참조된 열에 대해 고유한 제약 조건을 정의할 수 있다. 먼저 단일 열에 구현하고, 그다음 여러 열에 구현한다.

### 1) 단일 열 제약 조건
Person 엔터티에는 Address 엔터티를 참조하는 주소 필드가 있다. Person은 고유한 주소를 가져야 한다.

따라서 Person의 주소 필드에 고유한 제약 조건을 정의한다.

```java
@Column(unique = true)
private Address address;
```

이 제약 조건을 빠르게 확인한다.

```text
[main] DEBUG org.hibernate.SQL - alter table Person add constraint UK_7xo3hsusabfaw1373oox9uqoe unique (address)
```

참조 테이블 열에 여러 개의 열 제약 조건을 정의할 수도 있다.

### 2) 다중 열 제약 조건
열의 조합에 대한 고유 제약 조건을 지정할 수 있다. 테이블 제약 조건을 사용하여 그렇게 할 수 있다.

personNumber와 address에 대한 고유 제약 조건을 정의 하고 이를 uniqueConstraints 배열에 추가한다.

```java
@Entity
@Table(uniqueConstraints = 
  { //other constraints
  @UniqueConstraint(name = "UniqueNumberAndAddress", columnNames = { "personNumber", "address" })})
```

마지막으로 고유한 제약조건을 살펴본다.

```text
[main] DEBUG org.hibernate.SQL - alter table Person add constraint UniqueNumberAndAddress unique (personNumber, address)
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-unique-constraints](https://www.baeldung.com/jpa-unique-constraints)
