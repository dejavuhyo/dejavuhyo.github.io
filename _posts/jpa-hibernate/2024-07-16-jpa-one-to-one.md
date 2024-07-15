---
title: JPA 일대일 관계
author: dejavuhyo
date: 2024-07-16 08:44:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-one-to-one, jpa-일대일, 일대일-관계]
---

## 1. 설명
사용자 관리 시스템을 구축하고 있다고 가정한다. 그리고 상사가 각 사용자의 우편 주소를 저장하라고 요청한다. 사용자는 우편 주소가 하나 있고, 우편 주소에는 단 한 명의 사용자만 연결된다.

이는 일대일 관계의 예이며, 이 경우 사용자와 주소 엔터티 간의 관계이다.

## 2. 외래 키 사용

### 1) 외래 키 사용 모델링
외래 키 기반의 일대일 매핑을 나타내는 다음 ER 다이어그램을 확인한다.

![fk](/assets/img/2024-07-16-jpa-one-to-one/fk.png)

이 예에서 users의 address_id 열은 address의 [외래 키](https://en.wikipedia.org/wiki/Foreign_key) 이다.

### 2) JPA 외래 키 구현
먼저, User 클래스를 생성하고 적절하게 주석을 단다.

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;
    //...

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "address_id", referencedColumnName = "id")
    private Address address;

    // ... getters and setters
}
```

관련 엔터티 필드인 Address에 `@OneToOne` 주석을 추가했다.

또한, `@JoinColumn` 주석을 배치하여 주소 테이블의 기본 키에 매핑되는 users 테이블의 열 이름을 구성해야 한다. 이름을 제공하지 않으면 Hibernate는 [몇 가지 규칙](https://docs.jboss.org/hibernate/jpa/2.2/api/javax/persistence/JoinColumn.html)을 따라 기본 이름을 선택한다.

마지막으로, 다음 엔터티에서 `@JoinColumn` 주석을 사용하지 않는다. 이는 외래 키 관계의 소유 측에서만 필요하기 때문이다. 간단히 말해서, 외래 키 열을 소유한 사람은 `@JoinColumn` 주석을 얻는다.

Address 엔터티는 조금 더 간단해졌다.

```java
@Entity
@Table(name = "address")
public class Address {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;
    //...

    @OneToOne(mappedBy = "address")
    private User user;

    //... getters and setters
}
```

`@OneToOne` 주석도 여기에 넣어야 한다. 이는 양방향 관계이기 때문이다. 관계의 주소 측을 비소유 측이라고 한다.

## 3. 공유 기본 키 사용

### 1) 공유 기본 키 사용 모델링
이 전략에서는 새로운 열 address_id를 생성하는 대신 address 테이블의 기본 키 열(user_id)을 users 테이블의 외래 키로 표시한다.

![fk](/assets/img/2024-07-16-jpa-one-to-one/fk.png)

이러한 개체들이 서로 일대일 관계를 가지고 있다는 사실을 활용하여 저장 공간을 최적화했다.

### 2) JPA 공유 기본 키 구현
정의가 약간만 변경된다.

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;

    //...

    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL)
    @PrimaryKeyJoinColumn
    private Address address;

    //... getters and setters
}
```

```java
@Entity
@Table(name = "address")
public class Address {

    @Id
    @Column(name = "user_id")
    private Long id;

    //...

    @OneToOne
    @MapsId
    @JoinColumn(name = "user_id")
    private User user;
   
    //... getters and setters
}
```

mappedBy 속성은 이제 외래 키가 주소 테이블에 있으므로 User 클래스로 이동되었다. 또한 `@PrimaryKeyJoinColumn` 주석을 추가했는데, 이는 User 엔터티의 기본 키가 연관된 Address 엔터티의 외래 키 값으로 사용된다는 것을 나타낸다.

Address 클래스에서 `@Id` 필드를 정의해야 한다. 하지만 이는 user_id 열을 참조하며 더 이상 `@GeneratedValue` 주석을 사용하지 않는다. 또한 User를 참조하는 필드에 `@MapsId` 주석을 추가했는데, 이는 기본 키 값이 User 엔터티에서 복사됨을 나타낸다.

## 4. 조인 테이블 사용
일대일 매핑은 선택 사항과 필수 사항의 두 가지 유형이 있다. 지금까지는 필수 관계만 보았다.

이제 직원들이 워크스테이션과 연관되어 있다고 가정힌다. 일대일이지만, 때로는 직원이 워크스테이션을 가지고 있지 않을 수도 있고 그 반대의 경우도 있다.

### 1) 조인 테이블 사용 모델링
지금까지는 선택적 관계를 처리하기 위해 열에 null 값을 넣어야 했다.

일반적으로 조인 테이블을 고려할 때 다대다 관계를 생각 하지만 이 경우 조인 테이블을 사용하면 이러한 null 값을 제거하는데 도움이 될 수 있다.

![jt](/assets/img/2024-07-16-jpa-one-to-one/jt.png)

관계가 생길 때마다 emp_workstation 테이블에 항목을 만들고 null을 전혀 포함하지 않도록 한다.

### 2) JPA 조인 테이블 구현
첫 번째 예에서는 `@JoinColumn`을 사용했다. 이번에는 `@JoinTable`을 사용한다.

```java
@Entity
@Table(name = "employee")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;

    //...

    @OneToOne(cascade = CascadeType.ALL)
    @JoinTable(name = "emp_workstation", 
      joinColumns = 
        { @JoinColumn(name = "employee_id", referencedColumnName = "id") },
      inverseJoinColumns = 
        { @JoinColumn(name = "workstation_id", referencedColumnName = "id") })
    private WorkStation workStation;

    //... getters and setters
}
```

```java
@Entity
@Table(name = "workstation")
public class WorkStation {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;

    //...

    @OneToOne(mappedBy = "workStation")
    private Employee employee;

    //... getters and setters
}
```

`@JoinTable`은 관계를 유지하면서 조인 테이블 전략을 사용하도록 Hibernate에 지시한다.

또한 Employee는 조인 테이블 주석을 사용하기로 선택했기 때문에 이 관계의 소유자이다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-one-to-one](https://www.baeldung.com/jpa-one-to-one)
