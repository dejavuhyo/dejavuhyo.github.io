---
title: JPA Optimistic Locking
author: dejavuhyo
date: 2024-09-27 10:38:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-optimistic-locking, optimistic-locking, jpa-locking, jpa-낙관적-잠금, 낙관적-잠금]
---

## 1. 낙관적 잠금 이해
낙관적 잠금을 사용하려면 `@Version` 주석이 있는 속성을 포함하는 엔터티가 필요하다. 이를 사용하는 동안 데이터를 읽는 각 트랜잭션은 버전 속성의 값을 보유한다.

트랜잭션이 업데이트를 하기 전에 버전 속성을 다시 확인한다.

값이 그 사이에 변경된 경우 OptimisticLockException이 throw됩니다. 그렇지 않으면 트랜잭션은 업데이트를 커밋하고 값 버전 속성을 증가시킨다.

## 2. 비관적 잠금 vs 낙관적 잠금
낙관적 잠금과 대조적으로 JPA는 비관적 잠금을 제공한다. 이는 데이터에 대한 동시 액세스를 처리하기 위한 또 다른 메커니즘이다.

낙관적 잠금은 버전 속성을 확인하여 엔티티의 변경 사항을 감지하는데 기반한다. 동시 업데이트가 발생하면 OptmisticLockException이 발생한다. 그 후에 데이터 업데이트를 다시 시도할 수 있다.

이 메커니즘은 업데이트나 삭제보다 훨씬 더 많은 읽기를 하는 애플리케이션에 적합하다고 생각할 수 있다. 또한 엔티티를 일정 시간 동안 분리해야 하고 잠금을 유지할 수 없는 상황에서도 유용하다.

반면, 비관적 잠금 메커니즘은 데이터베이스 수준에서 엔터티를 잠그는 것을 포함한다.

각 트랜잭션은 데이터에 대한 잠금을 획득할 수 있다. 잠금을 유지하는 한, 어떤 트랜잭션도 잠긴 데이터를 읽거나, 삭제하거나, 업데이트할 수 없다.

비관적 잠금을 사용하면 교착 상태가 발생할 수 있다고 추정할 수 있다. 그러나 낙관적 잠금보다 더 큰 데이터 무결성을 보장한다.

## 3. 버전 속성
버전 속성은 `@Version` 주석이 있는 속성이다. 낙관적 잠금을 활성화하는데 필요하다.

샘플 엔티티 클래스이다.

```java
@Entity
public class Student {

    @Id
    private Long id;

    private String name;

    private String lastName;

    @Version
    private Integer version;

    // getters and setters

}
```

버전 속성을 선언할 때 따라야 할 몇 가지 규칙이 있다.

* 각 엔터티 클래스에는 버전 속성이 하나만 있어야 한다.

* 여러 테이블에 매핑된 엔터티의 경우 기본 테이블에 배치해야 한다.

* 버전 속성의 유형은 다음 중 하나여야 한다: `int`, `Integer`, `long`, `Long`, `short`, `Short`, `java.sql.Timestamp`

엔티티를 통해 버전 속성의 값을 검색할 수 있지만 업데이트하거나 증가시켜서는 안 된다. 지속성 공급자만이 이를 수행할 수 있으므로 데이터는 일관되게 유지된다.

지속성 제공자는 버전 속성이 없는 엔터티에 대한 낙관적 잠금을 지원할 수 있다. 그러나 낙관적 잠금으로 작업할 때는 항상 버전 속성을 포함하는 것이 좋다.

이러한 속성을 포함하지 않는 엔터티를 잠그려고 하고 지속성 공급자가 이를 지원하지 않는 경우 PersistenceException이 발생한다.

## 4. 잠금 모드
JPA는 두 가지 다른 낙관적 잠금 모드(및 두 가지 별칭)를 제공한다.

* OPTIMISTIC은 버전 속성이 포함된 모든 엔터티에 대해 낙관적 읽기 잠금을 얻는다.

* OPTIMISTIC_FORCE_INCREMENT는 OPTIMISTIC과 동일한 낙관적 잠금을 얻고 추가적으로 버전 속성 값을 증가시킨다.

* READ는 OPTIMISTIC의 동의어이다.

* WRITE는 OPTIMISTIC_FORCE_INCREMENT의 동의어이다.

위에 나열된 모든 유형은 LockModeType 클래스에서 찾을 수 있다.

### 1) OPTIMISTIC(READ)
이미 알고 있듯이 OPTIMISTIC과 READ 잠금 모드는 동의어이다. 그러나 JPA 사양은 새로운 애플리케이션에서 OPTIMISTIC을 사용할 것을 권장한다.

OPTIMISTIC 잠금 모드를 요청할 때마다 지속성 공급자는 데이터에 대한 더티 읽기뿐만 아니라 반복 불가능한 읽기도 방지한다.

간단히 말해서, 다른 트랜잭션이 데이터에 대한 수정을 커밋하지 못하도록 해야 한다.

* 업데이트 또는 삭제되었지만 커밋되지 않았다.

* 그동안 성공적으로 업데이트 또는 삭제되었다.

### 2) OPTIMISTIC_INCREMENT (WRITE)
이전과 마찬가지로 OPTIMISTIC_INCREMENT와 WRITE는 동의어이지만 전자가 더 바람직하다.

OPTIMISTIC_INCREMENT는 OPTIMISTIC 잠금 모드와 동일한 조건을 충족해야 한다. 또한 버전 속성의 값을 증가시킨다. 그러나 즉시 수행해야 하는지 아니면 커밋 또는 플러시까지 연기해야 ​​하는지는 지정되지 않았다.

OPTIMISTIC 잠금 모드가 요청 되면 지속성 공급자가 OPTIMISTIC_INCREMENT 기능을 제공할 수 있다는 것을 알아두는 것이 좋다.

## 5. 낙관적 잠금 사용
버전이 지정된 엔터티의 경우 낙관적 잠금이 기본적으로 사용 가능하다. 하지만 이를 명시적으로 요청하는 방법은 여러 가지가 있다.

### 1) Find
낙관적 잠금을 요청하려면 EntityManager의 find 메서드에 적절한 LockModeType을 인수로 전달할 수 있다.

```java
entityManager.find(Student.class, studentId, LockModeType.OPTIMISTIC);
```

### 2) Query
잠금을 활성화하는 또 다른 방법은 Query 객체의 setLockMode 메서드를 사용하는 것이다.

```java
Query query = entityManager.createQuery("from Student where id = :id");
query.setParameter("id", studentId);
query.setLockMode(LockModeType.OPTIMISTIC_INCREMENT);
query.getResultList()
```

### 3) 명시적 잠금
EntityManager의 잠금 메서드를 호출하여 잠금을 설정할 수 있다.

```java
Student student = entityManager.find(Student.class, id);
entityManager.lock(student, LockModeType.OPTIMISTIC);
```

### 4) Refresh
이전 메서드와 같은 방식으로 새로 고침 메서드를 호출할 수 있다.

```java
Student student = entityManager.find(Student.class, id);
entityManager.refresh(student, LockModeType.READ);
```

### 5) NamedQuery
마지막 옵션은 lockMode 속성과 함께 `@NamedQuery`를 사용하는 것다.

```java
@NamedQuery(name="optimisticLock",
  query="SELECT s FROM Student s WHERE s.id LIKE :id",
  lockMode = WRITE)
```

## 6. OptimisticLockException
지속성 공급자가 엔티티에서 낙관적 잠금 충돌을 발견하면 OptimisticLockException을 throw한다. 예외로 인해 활성 트랜잭션은 항상 롤백으로 표시된다는 점을 알고 있어야 한다.

OptimisticLockException에 어떻게 대응할 수 있는지 아는 것이 좋다. 편리하게도 이 예외에는 충돌하는 엔티티에 대한 참조가 포함되어 있다. 그러나 지속성 공급자가 모든 상황에서 이를 제공해야 하는 것은 아니다. 객체를 사용할 수 있다는 보장은 없다.

하지만 설명된 예외를 처리하는 권장 방법이 있다. 엔터티를 다시 검색해야 하며, 바람직하게는 새 트랜잭션에서 다시 로드하거나 새로 고침해야 한다. 그 후에 다시 한 번 업데이트를 시도할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-optimistic-locking](https://www.baeldung.com/jpa-optimistic-locking)
