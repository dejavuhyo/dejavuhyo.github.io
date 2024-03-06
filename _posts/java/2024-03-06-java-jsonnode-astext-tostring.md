---
title: Java JsonNode asText()와 toString() 차이
author: dejavuhyo
date: 2024-03-06 22:49:00 +0900
categories: [Language, Java]
tags: [java-jsonnode, jsonnode-astext, jsonnode-tostring, astext-tostring]
---

## 1. Dependencies
먼저 `pom.xml`에 [jackson-databind](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind) 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.14.1</version>
</dependency>
```

## 2. asText() 사용하기
Jackson ObjectMapper 개체와 Jackson JsonNode 개체를 사용한다.

`asText()` 메서드는 JsonNode의 텍스트 값을 문자열로 반환하지만 JsonNode 유형에 따라 다르게 작동한다.

* TextNode의 경우 노드의 텍스트 값을 반환한다.

* 숫자 노드의 경우 노드의 숫자 값을 문자열로 반환한다.

* BooleanNode 노드의 경우 true 또는 false를 반환한다.

* ArrayNode 및 ObjectNode의 경우 빈 문자열을 반환한다.

`asText()`의 아이디어는 조작 없이 노드의 값을 반환하는 것이다. `asText()`는 JsonNode의 추상 메서드이므로 모든 노드에는 각각 고유한 값을 반환하는 구현이 있다.

노드를 조작하지 않고는 불가능한 경우(예: 하위 노드로 인해) 빈 문자열을 반환한다.

```java
String json = "{\"name\":\"John\",\"age\":30}";
JsonNode node = new ObjectMapper().readTree(json);

String name = node.get("name").asText();
assertThat(name).isEqualTo("John");

String age = node.get("age").asText();
assertThat(age).isEqualTo("30");

String jsonText = node.asText();
assertThat(jsonText).isEmpty();
```

## 3. toString() 사용
`toString()` 메서드는 Object에서 재정의되고 JsonNode 데이터의 문자열 표현을 반환한다. 즉, 이 작업을 수행하면 따옴표와 이스케이프 문자가 포함된 JSON의 텍스트 표현(ObjectNode 및 ArrayNode의 경우 하위 노드 포함)이 반환된다.

이 방법은 파일, API 등에 쓸 수 있는 JSON 텍스트를 디버깅하거나 생성하는데 유용할 수 있다.

`asText()`와 달리 TextNode에 `toString()`을 적용하면 따옴표와 이스케이프 문자가 포함된 문자열이 반환된다.

```java
String json = "{\"name\":\"John\",\"age\":30}";
JsonNode node = new ObjectMapper().readTree(json);

String jsonString = node.toString();
assertThat(jsonString).isEqualTo("{\"name\":\"John\",\"age\":30}");

String name = node.get("name").toString();
assertThat(name).isEqualTo("\"John\"");

String age = node.get("age").toString();
assertThat(age).isEqualTo("30");
```

## 4. 특수문자
고려해야 할 또 다른 측면은 두 방법 모두 특수 문자와 유니코드 문자를 처리하는 방법이다. `asText()`는 결과 문자열이 유효한 JSON 인지 확인하기 위해 텍스트에 있는 큰따옴표 및 백슬래시와 같은 특수 문자를 자동으로 이스케이프한다.

`asText()`가 텍스트 값의 큰따옴표를 올바르게 이스케이프 해제하는 반면 `toString()`은 특수 문자를 자동으로 이스케이프하지 않는 방법을 보여주는 예이다. JSON 데이터에 특수 문자가 있는 경우 `toString()`을 사용하면 올바르게 구문 분석할 수 없는 잘못된 JSON 텍스트가 발생할 수 있다.

```java
String specialCharsJson = "{\"text\":\"Hello \\\"world\\\" !\"}";
JsonNode specialCharsNode = new ObjectMapper().readTree(specialCharsJson);

String specialCharsJsonAsText = specialCharsNode.get("text").asText();
assertThat(specialCharsJsonAsText).isEqualTo("Hello \"world\" !");

String specialCharsJsonToString = specialCharsNode.get("text").toString();
assertThat(specialCharsJsonToString).isEqualTo("\"Hello \\\"world\\\" !\"");
```

## [출처 및 참고]
* [https://www.baeldung.com/java-jsonnode-astext-vs-tostring](https://www.baeldung.com/java-jsonnode-astext-vs-tostring)
