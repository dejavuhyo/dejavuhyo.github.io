---
title: JPA/Hibernate Cascade 유형
author: dejavuhyo
date: 2024-07-23 14:43:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-cascade, hibernate-cascade, cascade-type, 캐스케이딩]
---

## 1. 캐스케이딩이란
엔티티 관계는 종종 다른 엔티티의 존재에 따라 달라진다. 예를 들어, Person-Address 관계이다. Person이 없으면 Address 엔티티는 자체적으로 의미가 없다. Person 엔티티를 삭제하면 Address 엔티티도 삭제되어야 한다.

이를 달성하는 방법은 캐스케이딩이다. 대상 엔터티에서 어떤 작업을 수행하면 동일한 작업이 연관된 엔터티에 적용된다.

### 1) JPA Cascade 유형
모든 JPA 관련 계단식 작업은 다음 항목을 포함하는 `javax.persistence.CascadeType` 열거형으로 표현된다.

* ALL

* PERSIST

* MERGE

* REMOVE

* REFRESH

* DETACH

### 2) Hibernate Cascade 유형
Hibernate는 JPA에서 지정한 것과 더불어 세 가지 추가 Cascade Types를 지원한다. 이러한 Hibernate 특정 Cascade Types는 `org.hibernate.annotations.CascadeType`에서 사용할 수 있다.

* REPLICATE

* SAVE_UPDATE

* LOCK

## 2. 캐스케이드 유형 간의 차이점

### 1) CascadeType.ALL
`CascadeType.ALL`은 Hibernate 관련 작업을 포함한 모든 작업을 부모 엔터티에서 자식 엔터티로 전파한다.

다음 예를 통해 확인한다.

```java
@Entity
public class Person {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;
    private String name;
    @OneToMany(mappedBy = "person", cascade = CascadeType.ALL)
    private List<Address> addresses;
}
```

OneToMany 연결에서 주석에 계단형 유형이 언급되어 있다.

이제 연관된 엔터티 address를 확인한다.

```java
@Entity
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;
    private String street;
    private int houseNumber;
    private String city;
    private int zipCode;
    @ManyToOne(fetch = FetchType.LAZY)
    private Person person;
}
```

### 2) CascadeType.PERSIST
persist 작업은 일시적 인스턴스를 지속시킨다. `CascadeType.PERSIST`는 부모 엔터티에서 자식 엔터티로 persist 작업을 전파한다. person 엔터티를 저장하면 address 엔터티도 저장된다.

지속 작업에 대한 테스트 사례이다.

```java
@Test
public void whenParentSavedThenChildSaved() {
    Person person = new Person();
    Address address = new Address();
    address.setPerson(person);
    person.setAddresses(Arrays.asList(address));
    session.persist(person);
    session.flush();
    session.clear();
}
```

위의 테스트 케이스를 실행하면 다음과 같은 SQL이 표시된다.

```text
Hibernate: insert into Person (name, id) values (?, ?)
Hibernate: insert into Address (city, houseNumber, person_id, street, zipCode, id) values (?, ?, ?, ?, ?, ?)
```

### 3) CascadeType.MERGE
병합 작업은 지정된 객체의 상태를 동일한 식별자를 가진 영구 객체로 복사한다. `CascadeType.MERGE`는 병합 작업을 부모 엔터티에서 자식 엔터티로 전파한다.

병합 작업을 테스트한다.

```java
@Test
public void whenParentSavedThenMerged() {
    int addressId;
    Person person = buildPerson("devender");
    Address address = buildAddress(person);
    person.setAddresses(Arrays.asList(address));
    session.persist(person);
    session.flush();
    addressId = address.getId();
    session.clear();

    Address savedAddressEntity = session.find(Address.class, addressId);
    Person savedPersonEntity = savedAddressEntity.getPerson();
    savedPersonEntity.setName("devender kumar");
    savedAddressEntity.setHouseNumber(24);
    session.merge(savedPersonEntity);
    session.flush();
}
```

테스트 케이스를 실행하면 병합 작업에서 다음과 같은 SQL이 생성된다.

```text
Hibernate: select address0_.id as id1_0_0_, address0_.city as city2_0_0_, address0_.houseNumber as houseNum3_0_0_, address0_.person_id as person_i6_0_0_, address0_.street as street4_0_0_, address0_.zipCode as zipCode5_0_0_ from Address address0_ where address0_.id=?
Hibernate: select person0_.id as id1_1_0_, person0_.name as name2_1_0_ from Person person0_ where person0_.id=?
Hibernate: update Address set city=?, houseNumber=?, person_id=?, street=?, zipCode=? where id=?
Hibernate: update Person set name=? where id=?
```

여기서는 병합 작업이 먼저 address와 person 엔터티를 모두 로드한 다음 `CascadeType.MERGE`의 결과로 두 엔터티를 모두 업데이트한다.

### 4) CascadeType.REMOVE
이름에서 알 수 있듯이, 제거 작업은 엔터티에 해당하는 행을 데이터베이스와 지속적 컨텍스트에서 제거한다.

`CascadeType.REMOVE`는 부모에서 자식 엔터티로 제거 작업을 전파한다. JPA의 `CascadeType.REMOVE`와 유사하게 Hibernate에만 있는 `CascadeType.DELETE`가 있다. 둘 사이에는 차이가 없다.

`CascadeType.Remove`를 테스트한다.

```java
@Test
public void whenParentRemovedThenChildRemoved() {
    int personId;
    Person person = buildPerson("devender");
    Address address = buildAddress(person);
    person.setAddresses(Arrays.asList(address));
    session.persist(person);
    session.flush();
    personId = person.getId();
    session.clear();

    Person savedPersonEntity = session.find(Person.class, personId);
    session.remove(savedPersonEntity);
    session.flush();
}
```

테스트 케이스를 실행하면 다음과 같은 SQL을 볼 수 있다.

```text
Hibernate: delete from Address where id=?
Hibernate: delete from Person where id=?
```

`CascadeType.REMOVE`로 인해 해당 person과 관련된 address도 제거되었다.

### 5) CascadeType.DETACH
detach 작업은 지속적 컨텍스트에서 엔티티를 제거한다. `CascadeType.DETACH`를 사용하면 자식 엔티티도 지속적 컨텍스트에서 제거된다.

실제로 확인 해본다.

```java
@Test
public void whenParentDetachedThenChildDetached() {
    Person person = buildPerson("devender");
    Address address = buildAddress(person);
    person.setAddresses(Arrays.asList(address));
    session.persist(person);
    session.flush();

    assertThat(session.contains(person)).isTrue();
    assertThat(session.contains(address)).isTrue();

    session.detach(person);
    assertThat(session.contains(person)).isFalse();
    assertThat(session.contains(address)).isFalse();
}
```

여기서는 person을 분리한 후에는 person도 address도 지속적인 컨텍스트에 존재하지 않는 것을 볼 수 있다.

### 6) CascadeType.LOCK
직관적이지 않게도 `CascadeType.LOCK`은 엔터티와 연관된 자식 엔터티를 지속적 컨텍스트에 다시 연결한다.

`CascadeType.LOCK`을 이해하기 위한 테스트 케이스이다.

```java
@Test
public void whenDetachedAndLockedThenBothReattached() {
    Person person = buildPerson("devender");
    Address address = buildAddress(person);
    person.setAddresses(Arrays.asList(address));
    session.persist(person);
    session.flush();

    assertThat(session.contains(person)).isTrue();
    assertThat(session.contains(address)).isTrue();

    session.detach(person);
    assertThat(session.contains(person)).isFalse();
    assertThat(session.contains(address)).isFalse();
    session.unwrap(Session.class)
      .buildLockRequest(new LockOptions(LockMode.NONE))
      .lock(person);

    assertThat(session.contains(person)).isTrue();
    assertThat(session.contains(address)).isTrue();
}
```

보시다시피 `CascadeType.LOCK`을 사용하면 엔티티 person과 연관된 address를 영구 컨텍스트에 다시 첨부할 수 있다.

### 7) CascadeType.REFRESH
새로 고침 작업은 데이터베이스에서 주어진 인스턴스의 값을 다시 읽는다. 어떤 경우에는 데이터베이스에 지속시킨 후 인스턴스를 변경할 수 있지만 나중에 해당 변경 사항을 실행 취소해야 한다.

그런 종류의 시나리오에서 이것은 유용할 수 있다. 이 작업을 `Cascade Type REFRESH`와 함께 사용하면 부모 엔터티가 새로 고쳐질 때마다 자식 엔터티도 데이터베이스에서 다시 로드된다.

`CascadeType.REFRESH` 테스트이다.

```java
@Test
public void whenParentRefreshedThenChildRefreshed() {
    Person person = buildPerson("devender");
    Address address = buildAddress(person);
    person.setAddresses(Arrays.asList(address));
    session.persist(person);
    session.flush();
    person.setName("Devender Kumar");
    address.setHouseNumber(24);
    session.refresh(person);
    
    assertThat(person.getName()).isEqualTo("devender");
    assertThat(address.getHouseNumber()).isEqualTo(23);
}
```

저장된 엔티티 person과 address에서 몇 가지 변경을 했다. person 엔티티를 새로 고치면 address도 새로 고쳐진다.

### 8) CascadeType.REPLICATE
복제 작업은 두 개 이상의 데이터 소스가 있고 데이터를 동기화하고 싶을 때 사용된다. `CascadeType.REPLICATE`를 사용하면 동기화 작업이 부모 엔터티에서 수행될 때마다 자식 엔터티에도 전파된다.

`CascadeType.REPLICATE` 테스트이다.

```java
@Test
public void whenParentReplicatedThenChildReplicated() {
    Person person = buildPerson("devender");
    person.setId(2);
    Address address = buildAddress(person);
    address.setId(2);
    person.setAddresses(Arrays.asList(address));
    session.unwrap(Session.class).replicate(person, ReplicationMode.OVERWRITE);
    session.flush();
    
    assertThat(person.getId()).isEqualTo(2);
    assertThat(address.getId()).isEqualTo(2);
}
```

`CascadeType.REPLICATE` 때문에 person 엔터티를 복제하면 연관된 address도 설정한 식별자와 함께 복제된다.

### 9) CascadeType.SAVE_UPDATE
`CascadeType.SAVE_UPDATE`는 동일한 작업을 연관된 자식 엔터티에 전파한다. save, update 및 saveOrUpdate와 같은 Hibernate 특정 작업을 사용할 때 유용하다.

`CascadeType.SAVE_UPDATE`가 어떻게 동작하는지 확인한다.

```java
@Test
public void whenParentSavedThenChildSaved() {
    Person person = buildPerson("devender");
    Address address = buildAddress(person);
    person.setAddresses(Arrays.asList(address));
    session.saveOrUpdate(person);
    session.flush();
}
```

`CascadeType.SAVE_UPDATE` 덕분에 위 테스트 케이스를 실행하면 person과 address가 모두 저장된 것을 볼 수 있다.

결과 SQL은 다음과 같다.

```text
Hibernate: insert into Person (name, id) values (?, ?)
Hibernate: insert into Address (city, houseNumber, person_id, street, zipCode, id) values (?, ?, ?, ?, ?, ?)
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-cascade-types](https://www.baeldung.com/jpa-cascade-types)
