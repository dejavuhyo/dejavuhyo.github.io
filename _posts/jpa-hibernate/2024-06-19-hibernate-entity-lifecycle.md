---
title: Hibernate Entity 수명 주기
author: dejavuhyo
date: 2024-06-19 17:33:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate, hibernate-entity, hibernate-lifecycle, 하이버네이트-수명-주기]
---

## 1. Helper Methods
여기에서 helper methods를 일관되게 사용한다.

* `HibernateLifecycleUtil.getManagedEntities(session)` - 세션의 내부 저장소에서 모든 관리되는 엔터티를 가져오는데 이를 사용한다.

* `DirtyDataInspector.getDirtyEntities()` - 이 메서드를 사용하여 'dirty'로 표시된 모든 엔터티 목록을 가져온다.

* `HibernateLifecycleUtil.queryCount(query)` -  내장된 데이터베이스에 대해 `count(*)` 쿼리를 수행하는 편리한 방법이다.

위의 모든 helper methods는 더 나은 가독성을 위해 정적으로 가져온다.

## 2. Persistence Context
엔터티 수명주기 주제를 다루기 전에 먼저 Persistence Context를 이해해야 한다.

간단히 말해서 Persistence Context는 클라이언트 코드와 데이터 저장소 사이에 위치한다. 영구 데이터가 엔터티로 변환되어 클라이언트 코드에서 읽고 변경할 수 있는 준비 영역이다.

이론적으로 지속성 컨텍스트는 작업 단위 패턴의 구현이다. 이는 로드된 모든 데이터를 추적하고, 해당 데이터의 변경 사항을 추적하며, 결국 비즈니스 트랜잭션이 끝날 때 모든 변경 사항을 데이터베이스에 다시 동기화하는 일을 담당한다.

JPA EntityManager와 Hibernate의 Session은 Persistence Context 개념을 구현한 것이다. 여기에서 Persistence Context를 표현하기 위해 Hibernate Session을 사용한다.

Hibernate 엔터티 수명주기 상태는 엔터티가 Persistence Context와 어떻게 관련되어 있는지 설명한다 .

## 3. Managed Entity
Managed Entity는 데이터베이스 테이블 행의 표현이다(해당 행이 아직 데이터베이스에 존재할 필요는 없음).

이는 현재 실행 중인 세션에 의해 관리 되며 세션에 대한 모든 변경 사항은 자동으로 추적되어 데이터베이스에 전파된다.

세션은 데이터베이스에서 엔터티를 로드하거나 분리된 엔터티를 다시 연결한다.

이해를 돕기 위해 몇 가지 코드이다.

샘플 애플리케이션은 FootballPlayer 클래스라는 하나의 엔터티를 정의한다. 시작 시 일부 샘플 데이터를 사용하여 데이터 저장소를 초기화한다.

```text
+-------------------+-------+
| Name              |  ID   |
+-------------------+-------+
| Cristiano Ronaldo | 1     |
| Lionel Messi      | 2     |
| Gianluigi Buffon  | 3     |
+-------------------+-------+
```

처음부터 Buffon의 이름을 바꾸고 싶다고 가정한다. Gigi Buffon 대신 그의 정식 이름 Gianluigi Buffon을 입력하고 싶다.

먼저 세션을 획득하여 작업 단위를 시작해야 한다.

```java
Session session = sessionFactory.openSession();
```

서버 환경에서는 컨텍스트 인식 프록시를 통해 코드에 세션을 주입할 수 있다. 원칙은 동일하게 유지된다. 작업 단위의 비즈니스 트랜잭션을 캡슐화하려면 세션이 필요하다.

다음으로, persistent 저장소에서 데이터를 로드하도록 세션에 지시한다.

```java
assertThat(getManagedEntities(session)).isEmpty();

List<FootballPlayer> players = s.createQuery("from FootballPlayer").getResultList();

assertThat(getManagedEntities(session)).size().isEqualTo(3);
```

Session을 처음 얻을 때 첫 번째 Assert 문에서 볼 수 있듯이 해당 영구 컨텍스트 저장소는 비어 있다.

다음으로, 데이터베이스에서 데이터를 검색하고, 데이터의 엔터티 표현을 생성하고, 마지막으로 사용할 엔터티를 반환하는 쿼리를 실행한다.

내부적으로 세션은 영구 컨텍스트 저장소에 로드하는 모든 엔터티를 추적한다. 세션의 내부 저장소에는 쿼리 뒤에 3개의 엔터티가 포함된다.

이제 Gigi의 이름을 변경한다.

```java
Transaction transaction = session.getTransaction();
transaction.begin();

FootballPlayer gigiBuffon = players.stream()
  .filter(p -> p.getId() == 3)
  .findFirst()
  .get();

gigiBuffon.setName("Gianluigi Buffon");
transaction.commit();

assertThat(getDirtyEntities()).size().isEqualTo(1);
assertThat(getDirtyEntities().get(0).getName()).isEqualTo("Gianluigi Buffon");
```

### 1) 동작
트랜잭션 `commit()` 또는 `flush()` 호출 시 세션은 추적 목록에서 dirty 엔터티를 찾아 상태를 데이터베이스에 동기화한다.

엔터티에서 무언가 변경되었음을 Session에 알리기 위해 메서드를 호출할 필요가 없었다. managed 엔터티이기 때문에 모든 변경 사항이 데이터베이스에 자동으로 전파된다.

managed 엔터티는 항상 영속적인 엔터티이다. 데이터베이스 행 표현이 아직 생성되지 않은 경우에도 데이터베이스 식별자가 있어야 한다. 즉, INSERT 문이 작업 단위의 끝을 보류 중이다.

## 4. Detached Entity
Detached 엔터티는 ID 값이 데이터베이스 행에 해당하는 일반 엔터티 POJO이다. 관리되는 엔터티와의 차이점은 지속성 컨텍스트에 의해 더 이상 추적되지 않는다는 것이다.

엔터티를 로드하는데 사용된 세션이 닫히거나 `Session.evict(entity)` 또는 `Session.clear()`를 호출하면 엔터티가 분리될 수 있다.

```java
FootballPlayer cr7 = session.get(FootballPlayer.class, 1L);

assertThat(getManagedEntities(session)).size().isEqualTo(1);
assertThat(getManagedEntities(session).get(0).getId()).isEqualTo(cr7.getId());

session.evict(cr7);

assertThat(getManagedEntities(session)).size().isEqualTo(0);
```

지속성 컨텍스트는 분리된 엔터티의 변경 사항을 추적하지 않는다.

```java
cr7.setName("CR7");
transaction.commit();

assertThat(getDirtyEntities()).isEmpty();
```

`Session.merge(entity)`/`Session.update(entity)`는 세션을 (재)연결할 수 있다.

```java
FootballPlayer messi = session.get(FootballPlayer.class, 2L);

session.evict(messi);
messi.setName("Leo Messi");
transaction.commit();

assertThat(getDirtyEntities()).isEmpty();

transaction = startTransaction(session);
session.update(messi);
transaction.commit();

assertThat(getDirtyEntities()).size().isEqualTo(1);
assertThat(getDirtyEntities().get(0).getName()).isEqualTo("Leo Messi");
```

### 1) Identity Field

```java
FootballPlayer gigi = new FootballPlayer();
gigi.setId(3);
gigi.setName("Gigi the Legend");
session.update(gigi);
```

위의 예에서는 생성자를 통해 일반적인 방법으로 엔터티를 인스턴스화했다. 필드에 값을 채웠고 ID를 3으로 설정했다. 이는 Gigi Buffon에 속한 영구 데이터의 ID에 해당한다. `update()`를 호출하면 다른 지속성 컨텍스트에서 엔터티를 로드한 것과 정확히 동일한 효과가 있다.

실제로 Session은 다시 연결된 엔터티가 어디에서 시작되었는지 구분하지 않는다.

HTML 양식 값에서 분리된 엔터티를 구성하는 것은 웹 애플리케이션에서 매우 일반적인 시나리오이다.

세션에 관한 한 분리된 엔터티는 ID 값이 영구 데이터에 해당하는 일반 엔터티일 뿐이다.

위의 예는 단지 데모 목적으로만 사용된다. 그리고 무엇을 하고 있는지 정확히 알아야 한다. 그렇지 않으면 업데이트하려는 필드에 값만 설정하고 나머지는 그대로 두는 경우 엔터티 전체에서 null 값이 발생할 수 있다(따라서 사실상 null).

## 5. Transient Entity
transient 엔터티는 단순히 영구 저장소에 표현이 없고 Session에 의해 관리되지 않는 엔터티 개체이다.

transient 엔터티의 일반적인 예는 생성자를 통해 새 엔터티를 인스턴스화하는 것이다.

transient 엔터티를 영구적으로 만들려면 `Session.save(entity)` 또는 `Session.saveOrUpdate(entity)`를 호출해야 한다.

```java
FootballPlayer neymar = new FootballPlayer();
neymar.setName("Neymar");
session.save(neymar);

assertThat(getManagedEntities(session)).size().isEqualTo(1);
assertThat(neymar.getId()).isNotNull();

int count = queryCount("select count(*) from Football_Player where name='Neymar'");

assertThat(count).isEqualTo(0);

transaction.commit();
count = queryCount("select count(*) from Football_Player where name='Neymar'");

assertThat(count).isEqualTo(1);
```

`Session.save(entity)`를 실행하자마자 엔터티에 ID 값이 할당되고 Session에 의해 관리된다. 그러나 작업 단위가 끝날 때까지 INSERT 작업이 지연될 수 있으므로 아직 데이터베이스에서 사용하지 못할 수도 있다.

## 6. Deleted Entity
`Session.delete(entity)`가 호출되고 세션이 해당 엔터티를 삭제 표시한 경우 엔터티는 삭제(제거) 상태이다. DELETE 명령 자체는 작업 단위가 끝날 때 실행될 수 있다.

```java
session.delete(neymar);

assertThat(getManagedEntities(session).get(0).getStatus()).isEqualTo(Status.DELETED);
```

그러나 엔터티는 작업 단위가 끝날 때까지 지속적 컨텍스트 저장소에 유지된다.

## [출처 및 참고]
* [https://www.baeldung.com/hibernate-entity-lifecycle](https://www.baeldung.com/hibernate-entity-lifecycle)
