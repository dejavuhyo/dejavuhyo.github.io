---
title: JPA INSERT 문
author: dejavuhyo
date: 2024-10-10 08:55:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-insert, insert-statement]
---

## 1. JPA에서 객체 유지
JPA에서는 일시적 상태에서 관리형 상태로 전환되는 모든 엔터티는 EntityManager에 의해 자동으로 처리된다.

EntityManager는 주어진 엔터티가 이미 존재하는지 확인한 다음 삽입할지 업데이트할지 결정한다. 이러한 자동 관리로 인해 JPA에서 허용되는 유일한 명령문은 SELECT, UPDATE 및 DELETE이다.

## 2. Common Model 정의
간단한 엔터티를 정의한다.

```java
@Entity
public class Person {

    @Id
    private Long id;
    private String firstName;
    private String lastName;

    // standard getters and setters, default and all-args constructors
}
```

또한, 구현에 사용할 저장소 클래스를 정의한다.

```java
@Repository
public class PersonInsertRepository {

    @PersistenceContext
    private EntityManager entityManager;

}
```

또한, `@Transactional` 주석을 적용하여 Spring에서 자동으로 트랜잭션을 처리한다. 이렇게 하면 EntityManager로 트랜잭션을 만들거나, 변경 사항을 커밋하거나, 예외가 발생할 경우 수동으로 롤백을 수행하는 것에 대해 걱정할 필요가 없다.

## 3. createNativeQuery
수동으로 생성된 쿼리의 경우 `EntityManager#createNativeQuery` 메서드를 사용할 수 있다. 이를 통해 JPA에서 지원하는 것뿐만 아니라 모든 유형의 SQL 쿼리를 생성할 수 있다. 저장소 클래스에 새 메서드를 추가한다.

```java
@Transactional
public void insertWithQuery(Person person) {
    entityManager.createNativeQuery("INSERT INTO person (id, first_name, last_name) VALUES (?,?,?)")
      .setParameter(1, person.getId())
      .setParameter(2, person.getFirstName())
      .setParameter(3, person.getLastName())
      .executeUpdate();
}
```

이 접근 방식에서는 열 이름을 포함하는 리터럴 쿼리를 정의하고 해당 값을 설정해야 한`다.

저장소를 테스트할 수 있다.

```java
@Test
public void givenPersonEntity_whenInsertedTwiceWithNativeQuery_thenPersistenceExceptionExceptionIsThrown() {
    Person person = new Person(1L, "firstname", "lastname");

    assertThatExceptionOfType(PersistenceException.class).isThrownBy(() -> {
        personInsertRepository.insertWithQuery(PERSON);
        personInsertRepository.insertWithQuery(PERSON);
    });
}
```

테스트에서 모든 작업은 데이터베이스에 새 항목을 삽입하려고 시도한다. 동일한 id를 가진 두 개의 엔터티를 삽입하려고 했기 때문에 두 번째 삽입 작업은 PersistenceException을 throw하여 실패한다.

Spring Data의 `@Query`를 사용하는 경우에도 원리는 동일하다.

## 4. persist
이전 예에서 삽입 쿼리를 만들었지만, 각 엔터티에 대한 리터럴 쿼리를 만들어야 했다. 이 접근 방식은 그다지 효율적이지 않으며 많은 보일러플레이트 코드를 생성한다.

대신 EntityManager의 persist 메서드를 활용할 수 있다.

이전 예제와 마찬가지로 사용자 정의 메서드로 저장소 클래스를 확장한다.

```java
@Transactional
public void insertWithEntityManager(Person person) {
    this.entityManager.persist(person);
}
```

접근 방식을 다시 테스트한다.

```java
@Test
public void givenPersonEntity_whenInsertedTwiceWithEntityManager_thenEntityExistsExceptionIsThrown() {
    assertThatExceptionOfType(EntityExistsException.class).isThrownBy(() -> {
        personInsertRepository.insertWithEntityManager(new Person(1L, "firstname", "lastname"));
        personInsertRepository.insertWithEntityManager(new Person(1L, "firstname", "lastname"));
    });
}
```

네이티브 쿼리를 사용하는 것과 달리, 열 이름과 해당 값을 지정할 필요가 없다. 대신, EntityManager가 대신해서 처리한다.

위 테스트에서는 더 특수화되어 persist에서 발생하는 슈퍼클래스 PersistenceException 대신 EntityExistsException이 발생하기를 기대한다.

반면에 이 예에서 Person의 새 인스턴스로 매번 insert 메서드를 호출 해야 한다. 그렇지 않으면 EntityManager가 이미 관리하고 있어서 업데이트 작업이 발생한다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-insert](https://www.baeldung.com/jpa-insert)
