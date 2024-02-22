---
title: Java Jackson으로 JSON Elements 제거
author: dejavuhyo
date: 2024-02-22 15:23:00 +0900
categories: [Language, Java]
tags: [java-jackson, json-elements, remove-elements, jackson-json-elements, 요소-제거]
---

## 1. 환경 설정
Jackson을 사용하려면 먼저 [jackson-dataformat-xml](https://mvnrepository.com/artifact/com.fasterxml.jackson.dataformat/jackson-dataformat-xml) 종속성을 pom.xml 파일에 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.15.2</version>
</dependency>
```

이 라이브러리를 사용하면 데이터 바인딩 API를 사용할 수 있다.

## 2. 키로 JSON Elements 제거
JSON을 제거할 때 가장 간단한 방법은 해당 키를 사용하는 것이다. Jackson은 이 작업을 용이하게 하는 여러 가지 방법을 제공한다. 일반적으로 사용되는 접근 방식 중 하나는 Jackson API의 JSON 노드를 나타내는 JsonNode 클래스를 사용하는 것이다.

키로 요소를 제거하려면 다음 단계를 따른다.

* Jackson ObjectMapper를 사용하여 JSON 문자열 또는 입력 스트림을 구문 분석한다.

* JSON 데이터를 JsonNode 객체로 변환한다.

* 원하는 요소를 제거하려면 JsonNode 객체에서 제거(String fieldName) 메서드를 사용한다.

* ObjectMapper를 사용하여 수정된 JsonNode를 다시 JSON 문자열로 변환한다.

다음과 같은 JSON 개체가 있다고 가정한다.

```json
{
    "name": "John",
    "age": 30,
    "city": "New York"
}
```

이 객체에서 age 속성을 제거하고 싶다. 다음은 해당 코드 조각이다.

```java
@Test
public void given_JsonData_whenUsingJackson_thenRemoveElementByKey() throws JsonProcessingException {
    String json = "{\"name\": \"John\", \"age\": 30, \"city\": \"New York\"}";
    ObjectMapper objectMapper = new ObjectMapper();
    JsonNode jsonNode = objectMapper.readTree(json);
    ObjectNode object = (ObjectNode) jsonNode;
    object.remove("age");
    String updatedJson = objectMapper.writeValueAsString(object);
    Assertions.assertEquals("{\"name\":\"John\",\"city\":\"New York\"}", updatedJson);
}
```

위의 테스트 방법에서는 JSON 개체에서 키 age가 있는 요소를 제거 하고 결과 JSON 문자열에 해당 요소가 포함되어 있지 않은지 확인한다.

예상되는 JSON 출력과 실제 JSON 출력은 동일해야 한다.

```json
{
    "name": "John",
    "city": "New York"
}
```

## 3. 조건에 따라 JSON Elements 제거
때로는 특정 조건에 따라 컬렉션에서 요소를 제거해야 하는 상황이 있다. 예를 들어, 특정 값이나 특정 유형을 가진 모든 요소를 ​​제거하려고 할 수 있다.

다행히도 Jackson은 이 목표를 달성하기 위한 여러 가지 방법을 제공한다. 한 가지 접근 방식은 JsonNode를 사용하고, 해당 요소를 반복하고, 주어진 조건을 충족하는 요소를 제거하는 것이다.

다음 JSON 개체가 있는 주어진 시나리오를 고려해 본다.

```json
{
    "name": "John",
    "age": 30,
    "city": "New York"
}
```

이 객체에서 숫자이고 값이 30인 모든 요소를 ​​제거하고 싶다. 이를 수행하는 방법을 살펴본다.

```java
@Test
public void given_JsonData_whenUsingJackson_thenRemoveElementsByCondition() throws JsonProcessingException {
    String json = "{\"name\": \"John\", \"age\": 30, \"city\": \"New York\"}";
    ObjectMapper objectMapper = new ObjectMapper();
    JsonNode jsonNode = objectMapper.readTree(json);
    Iterator<JsonNode> elements = jsonNode.elements();
    while (elements.hasNext()) {
        JsonNode element = elements.next();
        if (element.isNumber() && element.asInt() == 30) {
            elements.remove();
        }
    }
    String updatedJson = objectMapper.writeValueAsString(jsonNode);
    Assertions.assertEquals("{\"name\":\"John\",\"city\":\"New York\"}", updatedJson);
}
```

위의 예에서는 JsonNode의 요소를 반복하고 숫자이고 값이 30인 모든 요소를 ​​제거한다. 결과 JSON 문자열에는 제거된 요소가 포함되어 있지 않다.

예상되는 JSON 출력과 실제 JSON 출력은 동일해야 한다.

```json
{
    "name": "John",
    "city": "New York"
}
```

## 4. 복잡한 구조에서 JSON Elements 제거
어떤 경우에는 중첩된 객체나 배열로 구성된 복잡한 JSON 구조를 접할 수도 있다. 이러한 구조를 효율적으로 처리하려면 요구 사항에 따라 특정 요소를 제거할 수 있는 능력이 필요하다.

Jackson의 풍부한 API 세트를 사용하여 JsonNode 인스턴스의 요소를 반복하고 조건부 검사를 수행하여 제거할 요소를 식별할 수 있다.

중첩된 객체나 배열에서 요소를 제거하려면 다음 단계를 따른다.

* ObjectMapper를 사용하여 JSON 문자열 또는 입력 스트림을 구문 분석한다.

* get(String fieldName) 또는 path(String fieldName)과 같은 JsonNode 메서드를 사용하여 JSON 구조를 탐색하여 원하는 요소에 도달한다.

* 선택한 JsonNode에서 적절한 제거 방법(remove(String fieldName), Remove(int index) 등)을 사용한다.

* ObjectMapper를 사용하여 수정된 JsonNode를 다시 JSON 문자열로 변환한다.

복잡한 구조를 가진 중첩된 JSON 개체를 다루고 있다고 가정한다.

```json
{
    "name": "John",
    "details": {
        "age": 30,
        "city": "New York"
    }
}
```

세부정보 중첩 객체에서 키 age가 있는 요소를 제거한다.

```java
@Test
public void given_JsonData_whenUsingJackson_thenRemoveElementFromNestedStructure() throws JsonProcessingException {
    String json = "{\"name\": \"John\", \"details\": {\"age\": 30, \"city\": \"New York\"}}";
    ObjectMapper objectMapper = new ObjectMapper();
    JsonNode jsonNode = objectMapper.readTree(json);
    JsonNode detailsNode = jsonNode.path("details");
    ((ObjectNode) detailsNode).remove("age");
    String updatedJson = objectMapper.writeValueAsString(jsonNode);
    Assertions.assertEquals("{\"name\":\"John\",\"details\":{\"city\":\"New York\"}}", updatedJson);
}
```

위 코드에서는 중첩된 객체(details)에 액세스하고 키(age)를 사용하여 요소를 제거한다. 결과 JSON 문자열은 수정된 구조를 반영하며 예상되는 JSON 출력은 실제 JSON 출력과 동일해야 한다.

```json
{
    "name": "John",
    "details": {
        "city": "New York"
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-jackson-remove-json-elements](https://www.baeldung.com/java-jackson-remove-json-elements)
