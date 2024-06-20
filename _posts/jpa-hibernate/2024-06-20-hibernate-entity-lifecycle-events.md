---
title: Hibernate Entity 수명 주기 이벤트
author: dejavuhyo
date: 2024-06-20 16:22:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate, hibernate-entity, hibernate-lifecycle, lifecycle-events, 하이버네이트-수명-주기, 수명-주기-이벤트]
---

## 1. JPA Entity Lifecycle Events
JPA는 다음과 같이 호출되는 7개의 선택적 수명 주기 이벤트를 지정한다.

* 새로운 엔터티에 대해 지속성이 호출되기 전 - @PrePersist

* 새로운 엔터티에 대해 지속성이 호출된 후 - @PostPersist

* 엔터티가 제거되기 전 - @PreRemove

* 엔터티가 삭제된 후 - @PostRemove

* 업데이트 작업 전 - @PreUpdate

* 엔터티가 업데이트된 후 - @PostUpdate

* 엔터티가 로드된 후 - @PostLoad

수명 주기 이벤트 주석을 사용하는 방법에는 엔터티에 메서드에 주석을 추가하고 주석이 달린 콜백 메서드로 EntityListener를 만드는 두 가지 접근 방식이 있다. 동시에 두 가지를 모두 사용할 수도 있다. 위치에 관계없이 콜백 메서드는 void 반환 유형을 가져야 한다.

따라서 새 엔터티를 생성하고 저장소의 save 메서드를 호출하면 `@PrePersist`로 주석이 달린 메서드가 호출된 다음 레코드가 데이터베이스에 삽입되고 마지막으로 `@PostPersist` 메서드가 호출된다. `@GeneratedValue`를 사용하여 기본 키를 자동으로 생성하는 경우 해당 키를 `@PostPersist` 메서드에서 사용할 수 있을 것으로 예상할 수 있다.

`@PostPersist`, `@PostRemove` 및 `@PostUpdate` 작업의 경우 문서에서는 작업 발생 직후, 플러시 후 또는 트랜잭션 종료 시 이러한 이벤트가 발생할 수 있다고 언급한다.

`@PreUpdate` 콜백은 데이터가 실제로 변경된 경우, 즉 실행할 실제 SQL 업데이트 문이 있는 경우에만 호출된다는 점에 유의해야 한다. `@PostUpdate` 콜백은 실제로 변경된 사항이 있는지 여부에 관계없이 호출된다.

엔터티를 유지하거나 제거하기 위한 콜백에서 예외가 발생하면 트랜잭션이 롤백된다.

## 2. Entity 주석 달기
엔터티에서 직접 콜백 주석을 사용하는 것부터 시작한다. 이 예에서는 사용자 레코드가 변경 되면 로그 추적을 남기므로 콜백 메서드에 간단한 로깅 문을 추가한다.

또한 데이터베이스에서 사용자 이름을 로드한 후 사용자의 전체 이름을 조합했는지 확인하려고 한다. `@PostLoad`로 메소드에 주석을 달아서 그렇게 할 것이다.

User 엔터티를 정의하는 것부터 시작한다.

```java
@Entity
public class User {
    private static Log log = LogFactory.getLog(User.class);

    @Id
    @GeneratedValue
    private int id;
    
    private String userName;
    private String firstName;
    private String lastName;
    @Transient
    private String fullName;

    // Standard getters/setters
}
```

다음으로 UserRepository 인터페이스를 생성해야 한다.

```java
public interface UserRepository extends JpaRepository<User, Integer> {
    public User findByUserName(String userName);
}
```

이제 User 클래스로 돌아가서 콜백 메서드를 추가한다.

```java
@PrePersist
public void logNewUserAttempt() {
    log.info("Attempting to add new user with username: " + userName);
}
    
@PostPersist
public void logNewUserAdded() {
    log.info("Added user '" + userName + "' with ID: " + id);
}
    
@PreRemove
public void logUserRemovalAttempt() {
    log.info("Attempting to delete user: " + userName);
}
    
@PostRemove
public void logUserRemoval() {
    log.info("Deleted user: " + userName);
}

@PreUpdate
public void logUserUpdateAttempt() {
    log.info("Attempting to update user: " + userName);
}

@PostUpdate
public void logUserUpdate() {
    log.info("Updated user: " + userName);
}

@PostLoad
public void logUserLoad() {
    fullName = firstName + " " + lastName;
}
```

테스트를 실행하면 주석이 달린 메서드에서 나오는 일련의 로깅 문을 볼 수 있다. 또한 데이터베이스에서 사용자를 로드할 때 사용자의 전체 이름이 채워질 것으로 예상할 수 있다.

## 3. EntityListener 주석 달기
예제를 확장하고 별도의 EntityListener를 사용하여 업데이트 콜백을 처리한다. 모든 엔터티에 적용하려는 작업이 있는 경우 엔터티에 메서드를 배치하는 것보다 이 접근 방식을 선호할 수 있다.

User 테이블의 모든 활동을 기록하기 위해 AuditTrailListener를 생성한다.

```java
public class AuditTrailListener {
    private static Log log = LogFactory.getLog(AuditTrailListener.class);
    
    @PrePersist
    @PreUpdate
    @PreRemove
    private void beforeAnyUpdate(User user) {
        if (user.getId() == 0) {
            log.info("[USER AUDIT] About to add a user");
        } else {
            log.info("[USER AUDIT] About to update/delete user: " + user.getId());
        }
    }
    
    @PostPersist
    @PostUpdate
    @PostRemove
    private void afterAnyUpdate(User user) {
        log.info("[USER AUDIT] add/update/delete complete for user: " + user.getId());
    }
    
    @PostLoad
    private void afterLoad(User user) {
        log.info("[USER AUDIT] user loaded from database: " + user.getId());
    }
}
```

예제에서 볼 수 있듯이 메소드에 여러 개의 주석을 적용할 수 있다.

User 엔터티에서 클래스에 `@EntityListener` 주석을 추가한다.

```java
@EntityListeners(AuditTrailListener.class)
@Entity
public class User {
    //...
}
```

테스트를 실행하면 각 업데이트 작업에 대한 두 개의 로그 메시지 세트와 사용자가 데이터베이스에서 로드된 후의 로그 메시지를 받게 된다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-entity-lifecycle-events](https://www.baeldung.com/jpa-entity-lifecycle-events)
