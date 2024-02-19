---
title: Java JSON Array를 Java List로 변환
author: dejavuhyo
date: 2024-02-19 10:07:00 +0900
categories: [Language, Java]
tags: [java-json, array-list, json-array]
---

## 1. Gson 라이브러리 사용
Gson은 Java 개체를 JSON으로 직렬화 및 역직렬화하는데 널리 사용되는 JSON 라이브러리이다. JSON 배열을 List 객체로 변경하는 간단한 방법을 제공한다.

### 1) Gson Maven 의존성
프로젝트 종속성에 [Gson 라이브러리](https://mvnrepository.com/artifact/com.google.code.gson/gson)를 추가한다.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version>
</dependency>
```

### 2) JSON Array를 Java List로 변환
Gson을 사용하여 JSON 배열을 List로 변환하는 방법이다.

JSON 배열의 예이다.

```json
[
{\"id\":1,\"name\":\"Icecream\",\"description\":\"Sweet and cold\"},
{\"id\":2,\"name\":\"Apple\",\"description\":\"Red and sweet\"},
{\"id\":3,\"name\":\"Carrot\",\"description\":\"Good for eyes\"}
];
```

위의 JSON 배열은 Product 클래스 목록을 나타낸다.

```java
public class Product {
    private int id;
    private String name;
    private String description;

    public Product(int id, String name, String description) {
        this.id = id;
        this.name = name;
        this.description = description;
    }
    // getter and setter
}
```

JSON 배열이 있으므로 List로 변환한다.

```java
@Test
public void whenUsingGsonLibrary_thenCompareTwoProducts() {
    Gson gson = new Gson();
    Type listType = new TypeToken<List<Product>>() {}.getType();

    List<Product> gsonList = gson.fromJson(jsonArray, listType);
    Assert.assertEquals(1, gsonList.get(0).getId());
    Assert.assertEquals("Sweet and cold", gsonList.get(0).getDescription());
    Assert.assertEquals("Icecream", gsonList.get(0).getName());
}
```

먼저 JSON 직렬화 및 역직렬화를 위한 메서드를 제공하는 Gson 클래스의 인스턴스를 만든다.

TypeToken 클래스를 사용하여 대상 목록의 유형을 지정할 수 있다. 위의 예에서는 대상 유형을 `List<Product>`로 정의했다.

그런 다음 Gson 객체의 `fromJson()` 메서드를 사용하여 JSON 배열 String을 List로 변환한다.

JSON 배열을 List로 변환했으므로 어설션도 분석한다. 어설션에서는 String JSON 배열의 ID 또는 설명과 같은 특정 필드를 Product 클래스 목록을 나타내는 변환된 gsonList와 비교한다.

## 2. Jackson 라이브러리 사용
Jackson은 널리 사용되는 Java용 JSON 라이브러리 중 하나이다. Jackson 라이브러리를 사용하여 JSON 배열을 List로 변환한다.

### 1) Jackson Maven 의존성
프로젝트 종속성에 [Jackson 라이브러리](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind)를 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.2</version>
</dependency>
```

### 2) JSON Array를 Java List로 변환
Jackson을 사용하여 JSON 배열을 List로 변환하는 방법이다.

```java
@Test
public void whenUsingJacksonLibrary_thenCompareTwoProducts() throws JsonProcessingException {

    // The jsonArray is the same JSON array from the above example
    ObjectMapper objectMapper = new ObjectMapper();
    TypeReference<List<Product>> listType = new TypeReference<List<Product>>() {};

    List<Product> jacksonList = objectMapper.readValue(jsonArray, jacksonTypeReference);
    Assert.assertEquals(1, jacksonList.get(0).getId());
    Assert.assertEquals("Sweet and cold", jacksonList.get(0).getDescription());
    Assert.assertEquals("Icecream", jacksonList.get(0).getName());
}
```

데이터 조작을 위한 Jackson 라이브러리의 핵심 클래스 역할을 하는 ObjectMapper 클래스의 인스턴스를 만든다.

TypeReference 클래스를 사용하여 대상 목록의 유형을 지정할 수 있다. 위의 예에서는 대상 유형을 `List<Product>`로 정의했다.

그런 다음 ObjectMapper 객체의 `readValue()` 메서드를 사용하여 JSON 배열 String을 List로 변환한다.

마지막으로 String JSON 배열의 특정 필드를 jacksonList 해당 필드와 비교한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-convert-json-array-to-list](https://www.baeldung.com/java-convert-json-array-to-list)
