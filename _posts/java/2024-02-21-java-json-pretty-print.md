---
title: Java JSON Pretty-Print
author: dejavuhyo
date: 2024-02-21 11:07:00 +0900
categories: [Language, Java]
tags: [java-json, json-pretty-print, pretty-print, json-print]
---

## 1. Jackson을 이용한 JSON Pretty Print
Jackson을 사용하여 JSON을 예쁘게 인쇄하려면 `pom.xml` 파일에 다음 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.2</version>
</dependency>
```

### 1) On-Demand 예쁜 인쇄
요청에 따라 JSON을 예쁘게 인쇄하려면 `writeWithDefaultPrettyPrinter()` 메서드를 활용하면 된다.

```java
String uglyJsonString = "{\"one\":\"AAA\",\"two\":[\"BBB\",\"CCC\"],\"three\":{\"four\":\"DDD\",\"five\":[\"EEE\",\"FFF\"]}}"; 

public String prettyPrintJsonUsingDefaultPrettyPrinter(String uglyJsonString) {
    ObjectMapper objectMapper = new ObjectMapper();
    Object jsonObject = objectMapper.readValue(uglyString, Object.class);
    String prettyJson = objectMapper.writerWithDefaultPrettyPrinter().writeValueAsString(jsonObject);
    return prettyJson;
}
```

결과는 올바른 형식의 JSON 객체이다.

```java
@Test
public void shouldPrettyPrintJsonStringUsingDefaultPrettyPrinter() throws JsonProcessingException {
    JsonPrettyPrinter jsonPrettyPrinter = new JsonPrettyPrinter();
    String formattedJsonString = jsonPrettyPrinter.prettyPrintJsonUsingDefaultPrettyPrinter(uglyJsonString);
    System.out.println(formattedJsonString);
}
```

결과이다.

```json
{
    "one" : "AAA",
    "two" : [ "BBB", "CCC" ],
    "three" : {
        "four" : "DDD",
        "five" : [ "EEE", "FFF" ]
    }
}
```

### 2) Globally 예쁜 인쇄
`INDENT_OUTPUT` 설정을 전역적으로 활성화하면 보기 좋게 인쇄된 올바른 형식의 JSON 문자열을 생성할 수 있다. 이렇게 하면 시스템 전체의 JSON 출력이 읽기 쉬운 방식으로 일관되게 형식화된다.

`INDENT_OUTPUT` 설정을 전역적으로 활성화 한다.

```java
public String prettyPrintUsingGlobalSetting(String uglyJsonString) {
    ObjectMapper mapper = new ObjectMapper().enable(SerializationFeature.INDENT_OUTPUT);
    Object jsonObject = mapper.readValue(uglyJsonString, Object.class);
    String prettyJson = mapper.writeValueAsString(jsonObject);
    return prettyJson;
}
```

결과는 올바른 형식의 JSON 객체이다.

```java
@Test
public void shouldPrettyPrintJsonStringUsingGlobalSetting() throws JsonProcessingException {
    JsonPrettyPrinter jsonPrettyPrinter = new JsonPrettyPrinter();
    String formattedJsonString = jsonPrettyPrinter.prettyPrintUsingGlobalSetting(uglyJsonString);
    System.out.println(formattedJsonString);
}
```

결과이다.

```json
{
    "one" : "AAA",
    "two" : [ "BBB", "CCC" ],
    "three" : {
        "four" : "DDD",
        "five" : [ "EEE", "FFF" ]
    }
}
```

## 2. Gson을 사용하여 JSON Pretty Print
Gson Maven 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version>
</dependency>
```

JSON을 예쁘게 인쇄하려면 `GsonBuilder`의 `setPrettyPrinting()` 메서드를 사용한다.

```java
public String prettyPrintUsingGson(String uglyJson) {
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    JsonElement jsonElement = JsonParser.parseString(uglyJsonString);
    String prettyJsonString = gson.toJson(jsonElement);
    return prettyJsonString;
}
```

결과는 올바른 형식의 JSON 객체이다.

```java
@Test
public void shouldPrettyPrintJsonStringUsingGson() {
    JsonPrettyPrinter jsonPrettyPrinter = new JsonPrettyPrinter();
    String formattedJsonString = jsonPrettyPrinter.prettyPrintUsingGson(uglyJsonString);
    System.out.println(formattedJsonString);
}
```

결과이다.

```json
{
    "one": "AAA",
    "two": [
        "BBB",
        "CCC"
    ],
    "three": {
        "four": "DDD",
        "five": [
            "EEE",
            "FFF"
        ]
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-json-pretty-print](https://www.baeldung.com/java-json-pretty-print)
