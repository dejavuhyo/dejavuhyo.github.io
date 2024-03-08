---
title: Java @JsonIgnore vs @Transient
author: dejavuhyo
date: 2024-03-08 09:49:00 +0900
categories: [Language, Java]
tags: [java-jsonignore, java-transient, jsonignore-transient]
---

## 1. @JsonIgnore
`@JsonIgnore` 주석을 사용하여 직렬화 및 역직렬화 프로세스 중에 무시해야 하는 메서드나 필드를 지정한다. 이 마커 주석은 Jackson 라이브러리에 속한다.

관련성이 없거나 민감한 정보를 포함할 수 있는 필드를 제외하기 위해 이 주석을 적용하는 경우가 많다. 무시하려는 속성을 표시하기 위해 필드나 메서드에서 이를 사용한다.

먼저, 여러 필드가 있는 간단한 Person 클래스를 만든다.

```java
class Person implements Serializable {

    private Long id;

    private String firstName;

    private String lastName;

    // getters and setters
}
```

이제 Person 객체의 JSON 표현에 id 필드를 표시하고 싶지 않다고 가정한다. `@JsonIgnore` 주석을 사용하여 필드를 제외한다.

```java
class Person implements Serializable {

    @JsonIgnore
    private Long id;

    private String firstName;

    private String lastName;

    // getters and setters
}
```

마지막으로 ObjectMapper가 id 필드를 무시하는지 확인하는 테스트를 작성한다.

```java
@Test
void givenPerson_whenSerializing_thenIdFieldIgnored() 
  throws JsonProcessingException {

    Person person = new Person(1L, "My First Name", "My Last Name");
    String result = new ObjectMapper().writeValueAsString(person);

    assertThat(result, containsString("firstName"));
    assertThat(result, containsString("lastName"));
    assertThat(result, not(containsString("id")));
}
```

## 2. @Transient
반면에 JPA(Java Persistence API)가 객체를 데이터베이스에 매핑할 때 필드를 무시해야 함을 나타내기 위해 `@Transient` 주석을 사용한다. 이 주석으로 필드를 표시하면 JPA는 필드를 유지하지 않으며 데이터베이스에서 해당 값을 검색하지 않는다.

이제 User 클래스를 만든다.

```java
@Entity
@Table(name = "Users")
class User implements Serializable {

    @Id
    private Long id;

    private String username;

    private String password;

    private String repeatedPassword;

    // getters and setters
}
```

`@Transient` 주석이 있는 User 개체의 데이터베이스 표현에서 RepeatedPassword 필드를 제외한다.

```java
@Entity
@Table(name = "User")
class User implements Serializable {

    @Id
    private Long id;

    private String username;

    private String password;

    @Transient
    private String repeatedPassword;

    // getters and setters
}
```

User 객체를 데이터베이스에 유지하면 JPA는 RepeatPassword 값을 데이터베이스에 저장하지 않는다. 또한 JPA는 데이터베이스에서 객체를 로드할 때 해당 필드를 무시한다.

```java
@Test
void givenUser_whenSave_thenSkipTransientFields() {
    User user = new User(1L, "user", "newPassword123", "newPassword123");
    User savedUser = userRepository.save(user);

    assertNotNull(savedUser);
    assertNotNull(savedUser.getPassword());
    assertNull(savedUser.getRepeatedPassword());
}
```

그러나 `@Transient` 주석은 직렬화에서 필드를 제외하지 않는다.

```java
@Test
void givenUser_whenSerializing_thenTransientFieldNotIgnored() throws JsonProcessingException {

    User user = new User(1L, "user", "newPassword123", "newPassword123");
    String result = new ObjectMapper().writeValueAsString(user);

    assertThat(result, containsString("user"));
    assertThat(result, containsString("repeatedPassword"));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-jsonignore-vs-transient](https://www.baeldung.com/java-jsonignore-vs-transient)
