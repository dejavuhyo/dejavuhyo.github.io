---
title: Java Jackson Snake Case를 Camel Case로 역직렬화
author: dejavuhyo
date: 2024-03-14 09:50:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-snake-case, jackson-camel-case, jackson-deserialize]
---

## 1. Jackson 설치
`pom.xml` 파일에 [Jackson](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind) 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.13</version>
</dependency>
```

## 2. 기본값을 사용하여 역직렬화
User 클래스의 예이다.

```java
public class User {
    private String firstName;
    private String lastName;

    // standard getters and setters
}
```

Snake Case 명명 표준(소문자 이름은 `_`로 구분)을 사용하는 이 JSON을 로드한다.

```json
{
    "first_name": "Jackie",
    "last_name": "Chan"
}
```

먼저 ObjectMapper를 사용하여 이 JSON을 역직렬화 해야 한다.

```java
ObjectMapper objectMapper = new ObjectMapper();
User user = objectMapper.readValue(JSON, User.class);
```

그러나 이것을 시도하면 오류가 발생한다.

```text
com.fasterxml.jackson.databind.exc.UnrecognizedPropertyException: Unrecognized field "first_name" (class com.baeldung.jackson.snakecase.User), not marked as ignorable (2 known properties: "lastName", "firstName"])
```

불행하게도 Jackson은 JSON의 이름을 User의 필드 이름과 정확하게 일치시킬 수 없다.

다음으로 이 문제를 해결하는 세 가지 방법이다.

## 3. @JsonProperty Annotation 사용
클래스 필드에 `@JsonProperty` 주석을 사용하여 필드를 JSON의 정확한 이름에 매핑할 수 있다.

```java
public class UserWithPropertyNames {
    @JsonProperty("first_name")
    private String firstName;

    @JsonProperty("last_name")
    private String lastName;

    // standard getters and setters
}
```

이제 JSON을 UserWithPropertyNames로 역직렬화할 수 있다.

```java
ObjectMapper objectMapper = new ObjectMapper();
UserWithPropertyNames user = objectMapper.readValue(JSON, UserWithPropertyNames.class);
assertEquals("Jackie", user.getFirstName());
assertEquals("Chan", user.getLastName());
```

## 4. @JsonNaming Annotation 사용
다음으로 클래스에 `@JsonNaming` 주석을 사용할 수 있으며 모든 필드는 Snake Case를 사용하여 역직렬화된다.

```java
@JsonNaming(PropertyNamingStrategy.SnakeCaseStrategy.class)
public class UserWithSnakeStrategy {
    private String firstName;
    private String lastName;

    // standard getters and setters
}
```

그런 다음 JSON을 다시 역직렬화한다.

```java
ObjectMapper objectMapper = new ObjectMapper();
UserWithSnakeStrategy user = objectMapper.readValue(JSON, UserWithSnakeStrategy.class);
assertEquals("Jackie", user.getFirstName());
assertEquals("Chan", user.getLastName());
```

## 5. ObjectMapper 구성
마지막으로 ObjectMapper의 setPropertyNamingStrategy 메소드를 사용하여 모든 직렬화에 대해 구성할 수 있다.

```java
ObjectMapper objectMapper = new ObjectMapper()
  .setPropertyNamingStrategy(PropertyNamingStrategy.SNAKE_CASE);
User user = objectMapper.readValue(JSON, User.class);
assertEquals("Jackie", user.getFirstName());
assertEquals("Chan", user.getLastName());
```

보시다시피 이제 User 클래스에 주석이 없더라도 JSON을 원래 User 개체로 역직렬화할 수 있다.

다른 명명 규칙(예: kebab case)이 있으며 위의 솔루션도 마찬가지로 작동한다.

## [출처 및 참고]
* [https://www.baeldung.com/jackson-deserialize-snake-to-camel-case](https://www.baeldung.com/jackson-deserialize-snake-to-camel-case)
