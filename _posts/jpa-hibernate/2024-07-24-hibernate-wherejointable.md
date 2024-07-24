---
title: Hibernate @WhereJoinTable 주석
author: dejavuhyo
date: 2024-07-24 09:12:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate-wherejointable, wherejointable]
---

## 1. 기본 @ManyToMany 관계
간단한 `@ManyToMany` 관계로 도메인 모델 엔티티, 관계 엔티티, 샘플 테스트 데이터가 필요하다.

### 1) 도메인 모델
`@ManyToMany`로 연결된 두 개의 간단한 엔터티인 User와 Group이 있다.

```java
@Entity(name = "users")
public class User {

    @Id
    @GeneratedValue
    private Long id;
    private String name;

    @ManyToMany
    private List<Group> groups = new ArrayList<>();

    // standard getters and setters

}
```

```java
@Entity
public class Group {

    @Id
    @GeneratedValue
    private Long id;
    private String name;

    @ManyToMany(mappedBy = "groups")
    private List<User> users = new ArrayList<>();

    // standard getters and setters

}
```

보시다시피, User 엔티티는 두 개 이상의 Group 엔티티의 멤버가 될 수 있다. 마찬가지로, Group 엔티티는 두 개 이상의 User 엔티티를 포함할 수 있다.

### 2) 관계 엔티티
`@ManyToMany` 연결의 경우 관계 테이블이라고 하는 별도의 데이터베이스 테이블이 필요하다. 관계 테이블에는 최소한 두 개의 열이 있어야 한다. 관련 User 및 Group 엔터티의 기본 키이다.

두 개의 기본 키 열만을 사용하여 Hibernate 매핑은 이 관계 테이블을 표현할 수 있다.

하지만 관계 테이블에 추가 데이터를 넣어야 하는 경우 다대다 관계 자체에 대한 관계 엔터티도 정의해야 한다.

이를 위해 UserGroupRelation 클래스를 생성한다.

```java
@Entity(name = "r_user_group")
public class UserGroupRelation implements Serializable {

    @Id
    @Column(name = "user_id", insertable = false, updatable = false)
    private Long userId;

    @Id
    @Column(name = "group_id", insertable = false, updatable = false)
    private Long groupId;

}
```

여기서는 나중에 참조할 수 있도록 엔티티의 이름을 r_user_group으로 지정했다.

추가 데이터의 경우 각 Group에 대한 모든 User의 역할을 저장하려고 한다. 따라서 UserGroupRole 열거형을 만든다.

```java
public enum UserGroupRole {
    MEMBER, MODERATOR
}
```

다음으로, UserGroupRelation에 역할 속성을 추가한다.

```java
@Enumerated(EnumType.STRING)
private UserGroupRole role;
```

마지막으로, 제대로 구성하려면 User의 groups 컬렉션에 `@JoinTable` 주석을 추가해야 한다. 여기서는 UserGroupRelation의 엔티티 이름인 r_user_group을 사용하여 조인 테이블 이름을 지정한다.

```java
@ManyToMany
@JoinTable(
    name = "r_user_group",
    joinColumns = @JoinColumn(name = "user_id"),
    inverseJoinColumns = @JoinColumn(name = "group_id")
)
private List<Group> groups = new ArrayList<>();
```

###  3) 샘플 데이터
통합 테스트를 위해 몇 가지 샘플 데이터를 정의한다.

```java
public void setUp() {
    session = sessionFactory.openSession();
    session.beginTransaction();
    
    user1 = new User("user1");
    user2 = new User("user2");
    user3 = new User("user3");

    group1 = new Group("group1");
    group2 = new Group("group2");

    session.save(group1);
    session.save(group2);

    session.save(user1);
    session.save(user2);
    session.save(user3);

    saveRelation(user1, group1, UserGroupRole.MODERATOR);
    saveRelation(user2, group1, UserGroupRole.MODERATOR);
    saveRelation(user3, group1, UserGroupRole.MEMBER);

    saveRelation(user1, group2, UserGroupRole.MEMBER);
    saveRelation(user2, group2, UserGroupRole.MODERATOR);
}

private void saveRelation(User user, Group group, UserGroupRole role) {

    UserGroupRelation relation = new UserGroupRelation(user.getId(), group.getId(), role);
    
    session.save(relation);
    session.flush();
    session.refresh(user);
    session.refresh(group);
}
```

user1과 user2는 두 그룹에 있다. 또한 user1이 group1에서 MODERATOR인 반면, 동시에 group2에서 MEMBER 역할을 한다.

## 2. @ManyToMany 관계 가져오기
엔터티를 제대로 구성했으므로 User 엔터티의 그룹을 가져온다.

### 1) Simple Fetch
그룹을 가져오려면 활성 Hibernate 세션 내에서 User의 `getGroups()` 메서드를 호출하기만 하면 된다.

```java
List<Group> groups = user1.getGroups();
```

그룹에 대한 출력은 다음과 같다.

```text
[Group [name=group1], Group [name=group2]]
```

### 2) 관계 엔터티 사용자 정의 필터
`@WhereJoinTable` 주석을 사용하면 필터링된 그룹만 직접 가져올 수 있다.

moderatorGroups라는 새 속성을 정의 하고 `@WhereJoinTable` 주석을 추가한다. 이 속성을 통해 관련 엔터티에 액세스하면 사용자가 MODERATOR인 그룹만 포함된다.

MODERATOR 역할에 따라 그룹을 필터링하려면 SQL where 절을 추가해야 한다.

```java
@WhereJoinTable(clause = "role='MODERATOR'")
@ManyToMany
@JoinTable(
    name = "r_user_group",
    joinColumns = @JoinColumn(name = "user_id"),
    inverseJoinColumns = @JoinColumn(name = "group_id")
)
private List<Group> moderatorGroups = new ArrayList<>();
```

이렇게 하면 지정된 SQL where 절을 적용하여 그룹을 쉽게 얻을 수 있다.

```java
List<Group> groups = user1.getModeratorGroups();
```

출력은 사용자가 MODERATOR 역할만 가지고 있는 그룹이 될 것이다.

```text
[Group [name=group1]]
```

## [출처 및 참고]
* [https://www.baeldung.com/hibernate-wherejointable](https://www.baeldung.com/hibernate-wherejointable)
