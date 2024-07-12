---
title: JPA 엔티티 및 Serializable 인터페이스
author: dejavuhyo
date: 2024-07-12 14:23:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-entities-serializable, jpa-entities, entities-serializable]
---

## 1. Serializable 인터페이스
Serializable은 코어 Java에서 발견되는 몇 안 되는 마커 인터페이스 중 하나이다. 마커 인터페이스는 메서드나 상수가 없는 특수한 경우의 인터페이스이다.

객체 직렬화는 Java 객체를 바이트 스트림으로 변환하는 프로세스이다. 그런 다음 이러한 바이트 스트림을 와이어를 통해 전송하거나 영구 메모리에 저장할 수 있다. 역직렬화는 바이트 스트림을 가져와 Java 객체로 다시 변환하는 역 프로세스이다. 객체 직렬화(또는 역직렬화)를 허용하려면 클래스가 Serializable 인터페이스를 구현해야 한다. 그렇지 않으면 `java.io.NotSerializableException`이 발생한다. 직렬화는 RMI, JPA, EJB와 같은 기술에서 널리 사용된다.

## 2. JPA와 Serializable

### 1) JPA 사양
JPA의 핵심 부분 중 하나는 엔티티 클래스이다. 이러한 클래스를 엔티티(`@Entity` 주석 또는 XML 설명자)로 표시한다. 엔티티 클래스가 충족해야 하는 요구 사항이 몇가지 있으며, JPA 사양에 따르면 가장 우려하는 것은 다음과 같다.

> 엔티티 인스턴스가 분리된 객체로 값으로 전달되려면(예: 원격 인터페이스를 통해) 엔티티 클래스가 Serializable 인터페이스를 구현해야 한다.

실제로, 목적이 JVM 도메인을 벗어나는 것이라면 직렬화가 필요하다.

각 엔티티 클래스는 영구 필드와 속성으로 구성된다. 사양은 엔티티의 필드가 Java 기본형, Java 직렬화 가능 유형 또는 사용자 정의 직렬화 가능 유형일 수 있음을 요구한다.

엔티티 클래스에는 기본 키도 있어야 한다. 기본 키는 기본(단일 지속 필드) 또는 복합 키일 수 있다. 복합 키에는 여러 규칙이 적용되는데, 그 중 하나는 복합 키가 직렬화 가능해야 한다는 것 이다.

Hibernate, H2 인메모리 데이터베이스, UserId를 복합 키로 사용하는 User 도메인 객체를 사용하여 간단한 예를 만들어 본다.

```java
@Entity
public class User {
    @EmbeddedId UserId userId;
    String email;
    
    // constructors, getters and setters
}

@Embeddable
public class UserId implements Serializable{
    private String name;
    private String lastName;
    
    // getters and setters
}
```

통합 테스트를 사용하여 도메인 정의를 테스트할 수 있다.

```java
@Test
public void givenUser_whenPersisted_thenOperationSuccessful() {
    UserId userId = new UserId();
    userId.setName("John");
    userId.setLastName("Doe");
    User user = new User(userId, "johndoe@gmail.com");

    entityManager.persist(user);

    User userDb = entityManager.find(User.class, userId);
    assertEquals(userDb.email, "johndoe@gmail.com");
}
```

UserId 클래스가 Serializable 인터페이스를 구현하지 않으면 복합 키가 해당 인터페이스를 구현해야 한다는 구체적인 메시지와 함께 MappingException이 발생한다.

### 2) Hibernate @JoinColumn 주석
[Hibernate 공식 문서](https://hibernate.org/orm/documentation/6.5/)에서 Hibernate에서 매핑을 설명할 때, `@JoinColumn` 주석에서 referencedColumnName을 사용할 때 참조된 필드는 직렬화 가능해야 한다고 언급한다. 일반적으로 이 필드는 다른 엔터티의 기본 키이다. 복잡한 엔터티 클래스의 드문 경우 참조는 직렬화 가능해야 한다.

이메일 필드가 더 이상 문자열이 아니라 독립적인 엔티티인 이전 User 클래스를 확장해 본다. 또한 사용자를 참조하고 필드 유형이 있는 Account 클래스를 추가한다. 각 User는 서로 다른 유형의 여러 계정을 가질 수 있다. 이메일 주소로 검색하는 것이 더 자연스럽기 때문에 Account를 이메일로 매핑한다.

```java
@Entity
public class User {
    @EmbeddedId private UserId userId;
    private Email email;
}

@Entity
public class Email implements Serializable {
    @Id
    private long id;
    private String name;
    private String domain;
}

@Entity
public class Account {
    @Id
    private long id;
    private String type;
    @ManyToOne
    @JoinColumn(referencedColumnName = "email")
    private User user;
}
```

모델을 테스트하기 위해 사용자에 대한 두 개의 계정을 생성하고 이메일 객체로 쿼리를 실행하는 테스트를 작성한다.

```java
@Test
public void givenAssociation_whenPersisted_thenMultipleAccountsWillBeFoundByEmail() {
    // object creation 

    entityManager.persist(user);
    entityManager.persist(account);
    entityManager.persist(account2);

    List<Account> userAccounts = entityManager.createQuery("select a from Account a join fetch a.user where a.user.email = :email", Account.class)
      .setParameter("email", email)
      .getResultList();
    
    assertEquals(userAccounts.size(), 2);
}
```

> 참고: 사용자는 H2 데이터베이스의 예약어이므로 엔터티 이름에 사용할 수 없다.

Email 클래스가 Serializable 인터페이스를 구현하지 않으면 다시 MappingException이 발생 하지만 이번에는 다소 난해한 메시지인 "유형을 확인할 수 없습니다"가 표시된다.

### 3) 프레젠테이션 계층에 엔터티 노출
HTTP를 사용하여 와이어를 통해 객체를 전송할 때 일반적으로 이 목적을 위해 특정 DTO(데이터 전송 객체)를 만든다. DTO를 만들면 내부 도메인 객체를 외부 서비스에서 분리한다. DTO 없이 엔터티를 프레젠테이션 계층에 직접 노출하려면 엔터티가 직렬화 가능해야 한다.

HttpSession 객체를 사용하여 여러 페이지 방문에서 웹사이트를 식별하는데 도움이 되는 관련 데이터를 저장한다. 웹 서버는 우아하게 종료할 때 디스크에 세션 데이터를 저장하거나 클러스터 환경에서 다른 웹 서버로 세션 데이터를 전송할 수 있다. 엔터티가 이 프로세스의 일부인 경우 직렬화할 수 있어야 한다. 그렇지 않으면 NotSerializableException이 발생한다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-entities-serializable](https://www.baeldung.com/jpa-entities-serializable)
