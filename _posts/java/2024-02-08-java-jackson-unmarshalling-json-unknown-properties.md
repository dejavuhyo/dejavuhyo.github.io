---
title: Java Jackson Unmarshalling JSON Unknown Properties
author: dejavuhyo
date: 2024-02-08 10:39:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-unmarshalling, unmarshalling-json, 자바-잭슨, 잭슨-언마샬링, json-언마샬링]
---

## 1. Additional/Unknown Field를 사용하여 JSON 언마샬링
JSON 입력은 모양과 크기가 다양하며 대부분의 경우 이를 설정된 수의 필드를 사용하여 사전 정의된 Java 개체에 매핑해야 한다. 목표는 기존 Java 필드에 매핑할 수 없는 JSON 속성을 무시하는 것이다.

예를 들어 JSON을 다음 Java 엔터티로 언마샬링해야 한다고 가정한다.

```java
public class MyDto {

    private String stringValue;
    private int intValue;
    private boolean booleanValue;

    // standard constructor, getters and setters 
}
```

### 1) 알 수 없는 필드의 UnrecognizedPropertyException
간단한 Java 엔터티에 대해 알 수 없는 속성이 있는 JSON을 언마샬링하려고 하면 `com.fasterxml.jackson.databind.exc.UnrecognizedPropertyException`이 발생한다.

```java
@Test(expected = UnrecognizedPropertyException.class)
public void givenJsonHasUnknownValues_whenDeserializing_thenException()
  throws JsonParseException, JsonMappingException, IOException {
    String jsonAsString = 
        "{"stringValue":"a"," +
        ""intValue":1," +
        ""booleanValue":true," +
        ""stringValue2":"something"}";
    ObjectMapper mapper = new ObjectMapper();

    MyDto readValue = mapper.readValue(jsonAsString, MyDto.class);

    assertNotNull(readValue);
}
```

다음 예외로 인해 실패한다.

```text
com.fasterxml.jackson.databind.exc.UnrecognizedPropertyException: 
Unrecognized field "stringValue2" (class org.baeldung.jackson.ignore.MyDto), 
not marked as ignorable (3 known properties: "stringValue", "booleanValue", "intValue"])
```

### 2) ObjectMapper를 사용하여 알 수 없는 필드 처리
JSON에서 알 수 없는 속성을 무시하도록 전체 ObjectMapper를 구성할 수 있다.

```java
new ObjectMapper()
  .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
```

이러한 종류의 JSON을 사전 정의된 Java 엔터티로 읽을 수 있어야 한다.

```java
@Test
public void givenJsonHasUnknownValuesButJacksonIsIgnoringUnknowns_whenDeserializing_thenCorrect()
  throws JsonParseException, JsonMappingException, IOException {
 
    String jsonAsString = 
        "{"stringValue":"a"," +
        ""intValue":1," +
        ""booleanValue":true," +
        ""stringValue2":"something"}";
    ObjectMapper mapper = new ObjectMapper()
      .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);

    MyDto readValue = mapper.readValue(jsonAsString, MyDto.class);

    assertNotNull(readValue);
    assertThat(readValue.getStringValue(), equalTo("a"));
    assertThat(readValue.isBooleanValue(), equalTo(true));
    assertThat(readValue.getIntValue(), equalTo(1));
}
```

### 3) 클래스 수준에서 알 수 없는 필드 처리
전체 Jackson ObjectMapper 대신 단일 클래스를 알 수 없는 필드를 허용하도록 표시할 수도 있다.

```java
@JsonIgnoreProperties(ignoreUnknown = true)
public class MyDtoIgnoreUnknown { ... }
```

이전과 동일한 동작을 테스트할 수 있다. 알 수 없는 필드는 무시되고 알려진 필드만 매핑된다.

```java
@Test
public void givenJsonHasUnknownValuesButIgnoredOnClass_whenDeserializing_thenCorrect() 
  throws JsonParseException, JsonMappingException, IOException {
 
    String jsonAsString =
        "{"stringValue":"a"," +
        ""intValue":1," +
        ""booleanValue":true," +
        ""stringValue2":"something"}";
    ObjectMapper mapper = new ObjectMapper();

    MyDtoIgnoreUnknown readValue = mapper
      .readValue(jsonAsString, MyDtoIgnoreUnknown.class);

    assertNotNull(readValue);
    assertThat(readValue.getStringValue(), equalTo("a"));
    assertThat(readValue.isBooleanValue(), equalTo(true));
    assertThat(readValue.getIntValue(), equalTo(1));
}
```

## 2. 불완전한 JSON 언마샬링
알 수 없는 추가 필드와 마찬가지로 Java 클래스의 모든 필드를 포함하지 않는 불완전한 JSON을 언마샬링하는 것은 Jackson에서 문제가 되지 않는다.

```java
@Test
public void givenNotAllFieldsHaveValuesInJson_whenDeserializingAJsonToAClass_thenCorrect() 
  throws JsonParseException, JsonMappingException, IOException {
    String jsonAsString = "{"stringValue":"a","booleanValue":true}";
    ObjectMapper mapper = new ObjectMapper();

    MyDto readValue = mapper.readValue(jsonAsString, MyDto.class);

    assertNotNull(readValue);
    assertThat(readValue.getStringValue(), equalTo("a"));
    assertThat(readValue.isBooleanValue(), equalTo(true));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/jackson-deserialize-json-unknown-properties](https://www.baeldung.com/jackson-deserialize-json-unknown-properties)
