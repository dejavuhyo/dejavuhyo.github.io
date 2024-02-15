---
title: Java Jackson JsonNode를 ObjectNode로 변환
author: dejavuhyo
date: 2024-02-15 09:22:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-null, jackson-remove-null, 잭슨-null-제거, 잭슨-null]
---

## 1. JsonNodе 및 ObjеctNodе 이해
JsonNode는 JSON 트리의 노드를 나타내는 Jackson 라이브러리의 추상 클래스이다. 이는 모든 노드의 기본 클래스이며 objects, arrays, strings, numbers, booleans 및 null 값을 포함한 다양한 유형의 데이터를 저장할 수 있다. JsonNode 인스턴스는 변경할 수 없다. 즉, 해당 인스턴스에 속성을 설정할 수 없다.

ObjectNode는 특히 객체 노드를 나타내는 JsonNode의 변경 가능한 하위 클래스로 정의할 수 있다. 객체 내에서 key-value 쌍을 추가, 제거 및 수정하는 메서드를 제공하여 이러한 유형의 객체를 조작할 수 있다. 조작 방법 외에도 ObjectNode는 `asInt`, `asText` 및 `asBoolean`과 같은 편리한 접근자를 제공하여 객체 노드에서 해당 데이터 유형을 쉽게 검색할 수 있다.

## 2. 잭슨 가져오기
Jackson 라이브러리는 JSON 데이터를 효율적으로 읽고, 쓰고, 조작할 수 있는 다양한 기능을 제공한다.

`pom.xml`에 필요한 [종속성](https://mvnrepository.com/artifact/com.fasterxml.jackson.dataformat/jackson-dataformat-xml)을 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.14.2</version>
</dependency>
```

## 3. 변환 수행
간단한 JSON 개체를 정의한다.

```json
{
   "name":"John",
   "gender":"male",
   "company":"Baeldung",
   "isEmployee": true,
   "age": 30
}
```

코드에서 이를 문자열 값으로 선언한다.

```java
public static String jsonString = "{\"name\": \"John\", \"gender\": \"male\", \"company\": \"Baeldung\", \"isEmployee\": true, \"age\": 30}";
```

먼저 Jackon의 ObjеctMappеr 클래스를 활용하여 `ObjectMapper.readTree()` 메서드를 사용하여 이 문자열을 JsonNodе로 변환한다. 그런 다음 간단히 ObjеctNodе로 캐스팅할 수 있다.

```java
ObjectMapper objectMapper = new ObjectMapper();
JsonNode jsonNode = objectMapper.readTree(jsonString);
ObjectNode objectNode = (ObjectNode) jsonNode;
```

마지막으로 JsonNode에서 ObjеctNode로 변환한 후 데이터의 무결성을 확인하는 일련의 어설션을 통해 유효성 검사를 수행한다.

```java
assertEquals("John", objectNode.get("name").asText());
assertEquals("male", objectNode.get("gender").asText());
assertEquals("Baeldung", objectNode.get("company").asText());
assertTrue(objectNode.get("isEmployee").asBoolean());
assertEquals(30, objectNode.get("age").asInt());
```

## [출처 및 참고]
* [https://www.baeldung.com/java-jackson-jsonnode-objectnode](https://www.baeldung.com/java-jackson-jsonnode-objectnode)
