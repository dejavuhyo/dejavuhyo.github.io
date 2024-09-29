---
title: JPA Pessimistic Locking
author: dejavuhyo
date: 2024-09-30 08:46:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-pessimistic-locking, pessimistic-locking, jpa-locking, jpa-비관적-잠금, 비관적-잠금]
---

## 1. 잠금 모드
JPA 사양은 세 가지 비관적 잠금 모드를 정의한다.

* PESSIMISTIC_READ를 사용하면 공유 잠금을 얻고 데이터가 업데이트되거나 삭제되는 것을 방지할 수 있다.

* PESSIMISTIC_WRITE를 사용하면 배타적 잠금을 얻어 데이터를 읽거나, 업데이트하거나, 삭제하는 것을 방지할 수 있다.

* PESSIMISTIC_FORCE_INCREMENT는 PESSIMISTIC_WRITE와 유사하게 작동하며, 버전이 지정된 엔터티의 버전 속성을 추가로 증가시킨다.

모두 LockModeType 클래스의 정적 멤버이며 트랜잭션이 데이터베이스 잠금을 획득할 수 있도록 한다. 모두 트랜잭션이 커밋되거나 롤백될 때까지 유지된다.

한 번에 하나의 잠금만 얻을 수 있다. 불가능한 경우 PersistenceException이 발생한다.

### 1) PESSIMISTIC_READ
데이터만 읽고 더러운 읽기를 만나지 않으려면 PESSIMISTIC_READ(공유 잠금)를 사용할 수 있다. 하지만 업데이트나 삭제는 할 수 없다.

가끔 사용하는 데이터베이스가 PESSIMISTIC_READ 잠금을 지원하지 않는 경우가 있는데, 이럴 경우 대신 PESSIMISTIC_WRITE 잠금을 얻을 수 있다.

### 2) PESSIMISTIC_WRITE
데이터에 대한 잠금을 획득하고 변경해야 하는 모든 트랜잭션은 PESSIMISTIC_WRITE 잠금을 획득해야 한다. JPA 사양에 따르면 PESSIMISTIC_WRITE 잠금을 보유하면 다른 트랜잭션이 데이터를 읽거나, 업데이트하거나, 삭제할 수 없다.

일부 데이터베이스 시스템은 독자가 이미 차단된 데이터를 가져올 수 있도록 하는 다중 버전 동시성 제어를 구현한다.

### 3) PESSIMISTIC_FORCE_INCREMENT
이 잠금은 PESSIMISTIC_WRITE와 유사하게 작동하지만 버전이 지정된 엔터티, 즉 `@Version`으로 주석이 달린 속성을 가진 엔터티와 협력하기 위해 도입되었다.

버전화된 엔터티의 모든 업데이트는 PESSIMISTIC_FORCE_INCREMENT 잠금을 획득한 후에 이루어질 수 있다. 해당 잠금을 획득하면 버전 열이 업데이트된다.

PESSIMISTIC_FORCE_INCREMENT를 버전 없는 엔터티에 대해 지원하는지 여부는 지속성 공급자가 결정한다. 지원하지 않는 경우 PersistenceException을 throw한다.

### 4) Exceptions
비관적 잠금으로 작업하는 동안 어떤 예외가 발생할 수 있는지 아는 것이 좋습니다. JPA 사양은 다양한 유형의 예외를 제공합니다.

* PessimisticLockException은 잠금을 획득하거나 공유 잠금을 배타 잠금으로 변환하는데 실패하고 트랜잭션 수준의 롤백이 발생함을 나타낸다.

* LockTimeoutException은 잠금을 얻거나 공유 잠금을 배타적 잠금으로 변환하는 것이 시간 초과되어 명령문 수준 롤백을 초래하는 것을 나타낸다.

* PersistenceException은 지속성 문제가 발생했음을 나타낸다. PersistenceException과 NoResultException, NonUniqueResultException, LockTimeoutException 및 QueryTimeoutException을 제외한 하위 유형은 롤백할 활성 트랜잭션을 표시한다.

## 2. 비관적 잠금 사용
단일 레코드 또는 레코드 그룹에 대해 비관적 잠금을 구성하는 몇 가지 가능한 방법이 있다. JPA에서 이를 수행하는 방법이다.

### 1) Find
찾는 것이 아마도 가장 간단한 방법이다.

find 메서드에 LockModeType 객체를 매개변수로 전달하면 된다.

```java
entityManager.find(Student.class, studentId, LockModeType.PESSIMISTIC_READ);
```

### 2) Query
또한 Query 객체를 사용할 수도 있고 잠금 모드를 매개변수로 사용하여 setLockMode 세터를 호출할 수도 있다.

```java
Query query = entityManager.createQuery("from Student where studentId = :studentId");
query.setParameter("studentId", studentId);
query.setLockMode(LockModeType.PESSIMISTIC_WRITE);
query.getResultList()
```

### 3) 명시적 잠금
find 메서드로 검색된 결과를 수동으로 잠그는 것도 가능하다.

```java
Student resultStudent = entityManager.find(Student.class, studentId);
entityManager.lock(resultStudent, LockModeType.PESSIMISTIC_WRITE);
```

### 4) Refresh
새로 고침 메서드로 엔티티의 상태를 덮어쓰려면 잠금을 설정할 수도 있다.

```java
Student resultStudent = entityManager.find(Student.class, studentId);
entityManager.refresh(resultStudent, LockModeType.PESSIMISTIC_FORCE_INCREMENT);
```

### 5) NamedQuery
`@NamedQuery` 주석을 사용하면 잠금 모드도 설정할 수 있다.

```java
@NamedQuery(name="lockStudent",
  query="SELECT s FROM Student s WHERE s.id LIKE :studentId",
  lockMode = PESSIMISTIC_READ)
```

## 3. 잠금 범위
잠금 범위 매개변수는 잠긴 엔터티의 잠금 관계를 처리하는 방법을 정의한다. 쿼리에 정의된 단일 엔터티에만 잠금을 얻거나 추가로 관계를 차단할 수 있다.

범위를 구성하려면 PessimisticLockScope 열거형을 사용할 수 있다. 여기에는 NORMAL과 EXTENDED라는 두 가지 값이 포함된다.

EntityManager, Query, TypedQuery 또는 NamedQuery의 적절한 메서드에 인수로 PessimisticLockScope 값을 갖는 매개변수 'jakarta.persistence'를 전달하여 범위를 설정할 수 있다.

```java
Map<String, Object> properties = new HashMap<>();
map.put("jakarta.persistence", PessimisticLockScope.EXTENDED);

entityManager.find(Student.class, 1L, LockModeType.PESSIMISTIC_WRITE, properties);
```

### 1) PessimisticLockScope.NORMAL
`PessimisticLockScope.NORMAL`은 기본 범위이다. 이 잠금 범위를 사용하면 엔터티 자체를 잠근다. 연결된 상속과 함께 사용하면 조상도 잠근다.

두 개의 엔터티가 있는 샘플 코드이다.

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public class Person {

    @Id
    private Long id;
    private String name;
    private String lastName;

    // getters and setters
}

@Entity
public class Employee extends Person {

    private BigDecimal salary;

    // getters and setters
}
```

Employee에 대한 잠금을 얻고 싶을 때, 두 엔터티에 걸쳐 있는 SQL 쿼리를 관찰할 수 있다.

```java
SELECT t0.ID, t0.DTYPE, t0.LASTNAME, t0.NAME, t1.ID, t1.SALARY 
FROM PERSON t0, EMPLOYEE t1 
WHERE ((t0.ID = ?) AND ((t1.ID = t0.ID) AND (t0.DTYPE = ?))) FOR UPDATE
```

### 2) PessimisticLockScope.EXTENDED
EXTENDED 범위는 NORMAL과 동일한 기능을 포함한다. 또한 조인 테이블에서 관련 엔터티를 차단할 수 있다.

간단히 말해서, `@ElementCollection`이나 `@OneToOne`, `@OneToMany` 등으로 주석이 달린 엔터티와 `@JoinTable`로 작동한다.

`@ElementCollection` 주석이 있는 샘플 코드이다.

```java
@Entity
public class Customer {

    @Id
    private Long customerId;
    private String name;
    private String lastName;
    @ElementCollection
    @CollectionTable(name = "customer_address")
    private List<Address> addressList;

    // getters and setters
}

@Embeddable
public class Address {

    private String country;
    private String city;

    // getters and setters
}
```

고객 엔터티를 검색할 때 몇 가지 쿼리를 분석해 본다.

```java
SELECT CUSTOMERID, LASTNAME, NAME 
FROM CUSTOMER WHERE (CUSTOMERID = ?) FOR UPDATE

SELECT CITY, COUNTRY, Customer_CUSTOMERID 
FROM customer_address 
WHERE (Customer_CUSTOMERID = ?) FOR UPDATE
```

고객 테이블의 행과 조인 테이블의 행을 잠그는 FOR UPDATE 쿼리가 두 개 있다.

모든 지속성 공급자가 잠금 범위를 지원하지는 않는다.

## 4. 잠금 시간 초과 설정
잠금 범위를 설정하는 것 외에도, 또 다른 잠금 매개변수인 timeout을 조정할 수 있다. timeout 값은 LockTimeoutException이 발생할 때까지 잠금을 얻기 위해 기다리고 싶은 밀리초 수이다.

'jakarta.persistence.lock.timeout' 속성을 적절한 밀리초로 사용하여 잠금 범위와 유사하게 타임아웃 값을 변경할 수 있다.

또한 시간 초과 값을 0으로 변경하여 "대기 없음" 잠금을 지정할 수도 있다.

하지만 이런 방식으로 시간 초과 값을 설정하는 것을 지원하지 않는 데이터베이스 드라이버도 있다.

```java
Map<String, Object> properties = new HashMap<>(); 
map.put("jakarta.persistence.lock.timeout", 1000L); 

entityManager.find(Student.class, 1L, LockModeType.PESSIMISTIC_READ, properties);
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-pessimistic-locking](https://www.baeldung.com/jpa-pessimistic-locking)
