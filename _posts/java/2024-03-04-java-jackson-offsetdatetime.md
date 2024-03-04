---
title: Java Jackson OffsetDateTime 직렬화
author: dejavuhyo
date: 2024-03-04 23:00:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-offsetdatetime, offsetdatetime-직렬화]
---

## 1. 프로젝트 설정
OffsetDateTime 유형의 필드를 사용하여 클래스를 생성하고 이를 JSON으로 직렬화한다.

### 1) Dependency
`pom.xml`에 [Jackson Databind](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind) 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.14.1</version>
</dependency>
```

### 2) 코드 예
먼저 직렬화하려는 클래스를 정의한다.

```java
public class User {
    private OffsetDateTime createdAt;

    // constructors, getters and setters
}
```

다음으로 User 개체를 JSON으로 직렬화하는 메서드를 만든다.

```java
String serializeUser() throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper();
    User user = new User();
    user.setCreatedAt(OffsetDateTime.parse("2021-09-30T15:30:00+01:00"));

    return objectMapper.writeValueAsString(user);
}
```

위 메소드를 호출하면 아래와 같은 오류가 발생한다.

```text
Exception in thread "main" com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Java 8 date/time type `java.time.OffsetDateTime` not supported by default: add Module "com.fasterxml.jackson.datatype:jackson-datatype-jsr310" to enable handling (through reference chain: com.baeldung.offsetdatetime.User["createdAt"])
```

보시다시피 Jackson은 기본적으로 OffsetDateTime 직렬화를 지원하지 않는다. 이 문제를 해결하는 방법이다.

## 2. JavaTimeModule 등록
오류 메시지에서 제안한 대로 Jackson은 OffsetDateTime을 올바른 형식으로 직렬화하는데 사용할 수 있는 JavaTimeModule 이라는 모듈을 제공한다.

먼저 `pom.xml`에 [jackson-datatype-jsr310](https://mvnrepository.com/artifact/com.fasterxml.jackson.datatype/jackson-datatype-jsr310) 종속성을 포함한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-jsr310</artifactId>
    <version>2.14.1</version>
</dependency>
```

이제 객체를 직렬화하기 전에 이 모듈을 ObjectMapper에 등록할 수 있다.

```java
String serializeUser() throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper();
    objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
    objectMapper.registerModule(new JavaTimeModule());
    
    User user = new User();
    user.setCreatedAt(OffsetDateTime.parse("2021-09-30T15:30:00+01:00"));

    return objectMapper.writeValueAsString(user);
}
```

`registerModule()` 메소드를 사용하여 ObjectMapper에 JavaTimeModule을 등록했다. 또한 타임 스탬프가 아닌 입력과 동일한 형식으로 날짜를 가져오기 위해 `SerializationFeature.WRITE_DATES_AS_TIMESTAMPS` 기능을 비활성화했다.

메서드를 다시 호출하면 오류가 사라지고 출력에 직렬화된 날짜가 표시된다. JUnit 테스트를 사용하여 이를 테스트할 수 있다.

```java
@Test
void givenUser_whenSerialized_thenCreatedDateIsSerialized() throws JsonProcessingException {
    Assertions.assertEquals("{\"createdAt\":\"2021-09-30T15:30:00+01:00\"}", Main.serializeUser());
}
```

## 3. Custom Serialization 및 Deserializer
또 다른 방법은 OffsetDateTime에 대한 사용자 지정 직렬 변환기를 만드는 것이다. 날짜 형식을 사용자 정의하려는 경우에도 이 방법이 선호된다.

### 1) Custom Serializer
Jackson의 JsonSerializer 클래스를 확장하는 클래스를 생성하고 `serialize()` 메서드를 재정의하여 이를 수행할 수 있다.

```java
public class OffsetDateTimeSerializer extends JsonSerializer<OffsetDateTime> {
    private static final DateTimeFormatter DATE_TIME_FORMATTER
      = DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss XXX");
    
    @Override
    public void serialize(OffsetDateTime value, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) 
      throws IOException {
        if (value == null) {
            throw new IOException("OffsetDateTime argument is null.");
        }
        jsonGenerator.writeString(DATE_TIME_FORMATTER.format(value));
    }
}
```

위 코드에 대한 몇 가지 중요한 사항이다.

* 사용하려는 형식으로 [DateTimeFormatter](https://www.baeldung.com/java-datetimeformatter)를 만들었다. 여기서는 기본 형식과 다른 형식을 사용했다.

* 다음으로 DateTimeFormatter에서 `format()` 메서드를 호출하여 날짜 형식을 지정했다.

* 마지막으로 `writeString()` 메서드를 호출하여 형식화된 날짜를 JsonGenerator에 썼다.

이제 객체를 직렬화하기 전에 이 직렬 변환기를 ObjectMapper에 등록할 수 있다.

```java
String customSerialize() throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper();
    objectMapper.registerModule(new SimpleModule().addSerializer(OffsetDateTime.class, new OffsetDateTimeSerializer()));
    
    User user = new User();
    user.setCreatedAt(OffsetDateTime.parse("2021-09-30T15:30:00+01:00"));
    
    return objectMapper.writeValueAsString(user);
}
```

이제 직렬 변환기에 지정된 형식으로 날짜를 가져오는지 테스트할 수 있다.

```java
@Test
void givenUser_whenCustomSerialized_thenCreatedDateIsSerialized() throws JsonProcessingException {
    Assertions.assertEquals("{\"createdAt\":\"30-09-2021 15:30:00 +01:00\"}", Main.customSerialize());
}
```

### 2) Custom Deserializer
사용자 지정 직렬 변환기를 만들었으므로 JSON 문자열에서 날짜를 역직렬화하기 위한 사용자 지정 [역직렬 변환기](https://www.baeldung.com/jackson-deserialization)도 만들어야 한다. 이렇게 하지 않으면 동일한 InvalidDefinitionException이 다시 발생한다.

JsonDeserializer를 확장하는 클래스를 생성하고 `deserialize()` 메서드를 재정의하여 이를 수행할 수 있다.

```java
public class OffsetDateTimeDeserializer extends JsonDeserializer<OffsetDateTime> {
    private static final DateTimeFormatter DATE_TIME_FORMATTER
      = DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss XXX");
    
    @Override
    public OffsetDateTime deserialize(JsonParser jsonParser, DeserializationContext deserializationContext) 
      throws IOException {
        String dateAsString = jsonParser.getText();
        if (dateAsString == null) {
            throw new IOException("OffsetDateTime argument is null.");
        }
        return OffsetDateTime.parse(dateAsString, DATE_TIME_FORMATTER);
    }
}
```

직렬 변환기와 유사하게 사용하려는 형식으로 DateTimeFormatter를 만들었다. 결국 값을 반환하기 위한 OffsetDateTime 객체를 얻기 위해 포맷터를 구문 `parse()` 메소드에 전달했다.

객체를 역직렬화하려는 위치마다 이 역직렬 변환기를 ObjectMapper에 등록할 수 있다.

```java
String customDeserialize() throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper();
    objectMapper.registerModule(new SimpleModule().addDeserializer(OffsetDateTime.class, new OffsetDateTimeDeserializer()));
    
    String json = "{\"createdAt\":\"30-09-2021 15:30:00 +01:00\"}";
    User user = objectMapper.readValue(json, User.class);
    
    return returnedUser.getCreatedAt().toString();
}
```

출력에서 기본 OffsetDateTime 형식으로 날짜를 얻는다.

```java
@Test
void givenUser_whenCustomDeserialized_thenCreatedDateIsDeserialized() throws JsonProcessingException {
    Assertions.assertEquals("2021-09-30T15:30+01:00", Main.customDeserialize());
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-jackson-offsetdatetime](https://www.baeldung.com/java-jackson-offsetdatetime)
