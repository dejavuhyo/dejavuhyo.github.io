---
title: Hibernate 객체 삭제
author: dejavuhyo
date: 2024-10-14 15:10:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate-delete, 하이버네이트-삭제]
---

## 1. 객체 삭제의 다양한 방법
다음과 같은 경우에는 객체를 삭제할 수 있다.

* `EntityManager.remove`를 사용

* 다른 엔터티 인스턴스에서 삭제가 연쇄적으로 수행되는 경우

* orphanRemoval이 적용되는 경우

* delete JPQL 문을 실행

* 네이티브 쿼리를 실행

* 소프트 삭제 기술 적용(소프트 삭제된 엔터티를 `@Where` 절의 조건에 따라 필터링)

## 2. Entity Manager를 사용한 삭제
EntityManager를 사용한 삭제는 엔티티 인스턴스를 제거하는 가장 간단한 방법이다.

```java
Foo foo = new Foo("foo");
entityManager.persist(foo);
flushAndClear();

foo = entityManager.find(Foo.class, foo.getId());
assertThat(foo, notNullValue());
entityManager.remove(foo);
flushAndClear();

assertThat(entityManager.find(Foo.class, foo.getId()), nullValue());
```

예제에서는 필요할 때 지속성 컨텍스트를 플러시하고 지우기 위해 도우미 메서드를 사용한다.

```java
void flushAndClear() {
    entityManager.flush();
    entityManager.clear();
}
```

`EntityManager.remove` 메서드를 호출한 후, 제공된 인스턴스는 제거된 상태로 전환되고 다음 플러시 시에 데이터베이스에서 연관된 삭제가 발생한다.

삭제된 인스턴스는 PERSIST 작업이 적용된 경우 다시 유지된다. 일반적인 실수는 PERSIST 작업이 제거된 인스턴스에 적용되었다는 것을 무시하는 것이다(일반적으로 플러시 시간에 다른 인스턴스에서 캐스케이드되기 때문). JPA 사양의 섹션 3.2.2에서는 이러한 경우 해당 인스턴스를 다시 유지해야 한다고 명시하고 있기 때문이다.

이를 Foo에서 Bar로의 `@ManyToOne` 연결을 정의하여 설명한다.

```java
@Entity
public class Foo {
    @ManyToOne(fetch = FetchType.LAZY, cascade = CascadeType.ALL)
    private Bar bar;

    // other mappings, getters and setters
}
```

지속성 컨텍스트에 로드된 Foo 인스턴스에서 참조하는 Bar 인스턴스를 삭제하는 경우 Bar 인스턴스는 데이터베이스에서 제거되지 않는다.

```java
Bar bar = new Bar("bar");
Foo foo = new Foo("foo");
foo.setBar(bar);
entityManager.persist(foo);
flushAndClear();

foo = entityManager.find(Foo.class, foo.getId());
bar = entityManager.find(Bar.class, bar.getId());
entityManager.remove(bar);
flushAndClear();

bar = entityManager.find(Bar.class, bar.getId());
assertThat(bar, notNullValue());

foo = entityManager.find(Foo.class, foo.getId());
foo.setBar(null);
entityManager.remove(bar);
flushAndClear();

assertThat(entityManager.find(Bar.class, bar.getId()), nullValue());
```

제거된 Bar가 Foo에 의해 참조되는 경우, PERSIST 작업은 Foo에서 Bar로 계단식으로 연결된다. 왜냐하면 연관이 `cascade = CascadeType.ALL`로 표시되고 삭제가 예약되지 않았기 때문이다. 이것이 일어나고 있는지 확인하기 위해 org.hibernate 패키지에 대한 추적 로그 수준을 활성화 하고 예약되지 않은 엔티티 삭제와 같은 항목을 검색 할 수 있다.

## 3. 계단식 삭제
부모가 제거되면 삭제가 자식 엔터티로 전파될 수 있다.

```java
Bar bar = new Bar("bar");
Foo foo = new Foo("foo");
foo.setBar(bar);
entityManager.persist(foo);
flushAndClear();

foo = entityManager.find(Foo.class, foo.getId());
entityManager.remove(foo);
flushAndClear();

assertThat(entityManager.find(Foo.class, foo.getId()), nullValue());
assertThat(entityManager.find(Bar.class, bar.getId()), nullValue());
```

여기서 bar는 제거되었다. 왜냐하면 foo에서 제거가 계단식으로 이루어졌고, 모든 수명 주기 작업이 Foo에서 Bar로 계단식으로 이루어지도록 연관이 선언되었기 때문이다.

`@ManyToMany` 연관에서 REMOVE 작업을 캐스케이드하는 것은 거의 항상 버그다. 왜냐하면 다른 부모 인스턴스와 연관될 수 있는 자식 인스턴스를 제거하는 것을 트리거하기 때문이다. 이는 `CascadeType.ALL`에도 적용되는데, REMOVE 작업을 포함한 모든 작업이 캐스케이드되어야 함을 의미하기 때문이다.

## 4. Orphans 제거
orphanRemoval 지시어는 연관된 엔터티 인스턴스가 부모에서 분리되거나 부모가 제거될 때 제거되어야 함을 선언한다.

Bar에서 Baz까지의 이러한 연결을 정의하여 이를 보여준다.

```java
@Entity
public class Bar {
    @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Baz> bazList = new ArrayList<>();

    // other mappings, getters and setters
}
```

그러면 부모 Bar 인스턴스 목록에서 제거되면 Baz 인스턴스도 자동으로 삭제된다.

```java
Bar bar = new Bar("bar");
Baz baz = new Baz("baz");
bar.getBazList().add(baz);
entityManager.persist(bar);
flushAndClear();

bar = entityManager.find(Bar.class, bar.getId());
baz = bar.getBazList().get(0);
bar.getBazList().remove(baz);
flushAndClear();

assertThat(entityManager.find(Baz.class, baz.getId()), nullValue());
```

orphanRemoval 작업의 의미는 영향을 받는 자식 인스턴스에 직접 적용된 REMOVE 작업과 완전히 유사하다. 즉, REMOVE 작업은 중첩된 자식으로 더욱 확장된다. 결과적으로 다른 인스턴스가 제거된 인스턴스를 참조하지 않도록 해야 한다(그렇지 않으면 다시 유지된다).

## 5. JPQL 문장을 사용한 삭제
Hibernate는 DML 스타일 삭제 작업을 지원한다.

```java
Foo foo = new Foo("foo");
entityManager.persist(foo);
flushAndClear();

entityManager.createQuery("delete from Foo where id = :id")
  .setParameter("id", foo.getId())
  .executeUpdate();

assertThat(entityManager.find(Foo.class, foo.getId()), nullValue());
```

DML 스타일의 JPQL 문은 이미 지속성 컨텍스트에 로드된 엔터티 인스턴스의 상태나 수명 주기에 영향을 미치지 않으므로 영향을 받는 엔터티를 로드하기 전에 실행하는 것이 좋다.

## 6. 네이티브 쿼리를 사용한 삭제
때때로 Hibernate에서 지원하지 않거나 데이터베이스 공급업체에 특정한 것을 달성하기 위해 네이티브 쿼리로 돌아가야 한다. 네이티브 쿼리로 데이터베이스의 데이터를 삭제할 수도 있다.

```java
Foo foo = new Foo("foo");
entityManager.persist(foo);
flushAndClear();

entityManager.createNativeQuery("delete from FOO where ID = :id")
  .setParameter("id", foo.getId())
  .executeUpdate();

assertThat(entityManager.find(Foo.class, foo.getId()), nullValue());
```

동일한 권장 사항이 JPA DML 스타일 명령문과 동일한 기본 쿼리에 적용된다. 즉, 기본 쿼리는 쿼리 실행 전에 지속성 컨텍스트에 로드된 엔터티 인스턴스의 상태나 수명 주기에 영향을 미치지 않는다.

## 7. 소프트 삭제
감사 목적과 기록 보관 때문에 데이터베이스에서 데이터를 제거하는 것은 바람직하지 않은 경우가 많다. 이런 상황에서는 소프트 삭제라는 기술을 적용할 수 있다. 기본적으로 행을 제거됨으로 표시하고 데이터를 검색할 때 필터링한다.

소프트 삭제 가능한 엔터티를 읽는 모든 쿼리에서 where 절에 중복된 조건이 많이 포함되지 않도록 하기 위해 Hibernate는 엔터티에 배치할 수 있는 `@Where` 주석을 제공하며, 이 주석에는 해당 엔터티에 대해 생성된 SQL 쿼리에 자동으로 추가되는 SQL 조각이 포함된다.

이를 보여주기 위해 `@Where` 주석과 DELETED라는 열을 Foo 엔터티에 추가한다.

```java
@Entity
@Where(clause = "DELETED = 0")
public class Foo {
    // other mappings

    @Column(name = "DELETED")
    private Integer deleted = 0;
    
    // getters and setters

    public void setDeleted() {
        this.deleted = 1;
    }
}
```

다음 테스트는 모든 것이 예상대로 작동하는지 확인한다.

```java
Foo foo = new Foo("foo");
entityManager.persist(foo);
flushAndClear();

foo = entityManager.find(Foo.class, foo.getId());
foo.setDeleted();
flushAndClear();

assertThat(entityManager.find(Foo.class, foo.getId()), nullValue());
```

## [출처 및 참고]
* [https://www.baeldung.com/delete-with-hibernate](https://www.baeldung.com/delete-with-hibernate)
