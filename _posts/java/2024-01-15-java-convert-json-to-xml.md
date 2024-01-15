---
title: Java JSON을 XML로 변환
author: dejavuhyo
date: 2024-01-15 16:00:00 +0900
categories: [Language, Java]
tags: [java-xml, convert-xml, json-xml, xml-변환]
---

## 1. JSON-Java Library
SON-Java 라이브러리는 JSON을 XML로 변환하는 간단한 방법을 제공한다.

### 1) Dependency
`pom.xml`에 JSON-Java 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20230227</version>
</dependency>
```

### 2) Code Example
JSON 문자열을 XML로 변환하는 테스트 예제이다.

```java
@Test
public void givenJsonString_whenConvertToXMLUsingJsonJava_thenConverted() {
    String jsonString = "{\"name\":\"John\", \"age\":20, \"address\":{\"street\":\"Wall Street\", \"city\":\"New York\"}}";
    JSONObject jsonObject = new JSONObject(jsonString);
    String xmlString = XML.toString(jsonObject);
    Assertions.assertEquals("<address><city>New York</city><street>Wall Street</street></address><name>John</name><age>20</age>", xmlString);
}
```

`XML.toString()` 메서드를 사용하여 JSON 문자열을 XML로 변환할 수 있다. 이 메서드는 JSONObject를 인수로 사용하고 XML 문자열을 반환한다.

이 메서드는 각 키가 XML 태그로 변환되고 값이 태그의 텍스트 콘텐츠가 되는 압축 XML 문자열을 생성한다.

## 2. Jackson
Jackson은 널리 사용되는 Java용 JSON 라이브러리이다. JSON을 XML로 변환하는 데에도 사용할 수 있다.

### 1) Dependency
`pom.xml`에 Jackson 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.2</version>
</dependency>
```

### 2) Code Example
Jackson을 사용하여 JSON 문자열을 XML로 변환하는 테스트이다.

```java
@Test
public void givenJsonString_whenConvertToXMLUsingJackson_thenConverted() throws JsonProcessingException {
    String jsonString = "{\"name\":\"John\", \"age\":20, \"address\":{\"street\":\"Wall Street\", \"city\":\"New York\"}}";
    ObjectMapper objectMapper = new ObjectMapper();
    JsonNode jsonNode = objectMapper.readTree(jsonString);
    String xmlString = new XmlMapper().writeValueAsString(jsonNode);
    Assertions.assertEquals("<ObjectNode><name>John</name><age>20</age><address><street>Wall Street</street><city>New York</city></address></ObjectNode>", xmlString);
}
```

XmlMapper 클래스를 사용하여 JSON 문자열을 XML로 변환할 수 있다. 클래스에는 JsonNode를 인수로 사용하고 XML 문자열을 반환하는 `writeValueAsString()` 메서드가 있다. 또한 출력 태그는 ObjectNode 태그로 래핑된다.

### 3) 출력 사용자 정의
출력 XML 문자열의 형식이 지정되지 않았고 XML 선언도 없으며 루트 태그가 ObjectNode임을 확인했다. 이는 XML 표준을 따르지 않기 때문에 출력을 더 읽기 쉽고 표준을 준수하도록 사용자 정의할 수 있다.

출력을 사용자 정의하기 위해 XmlMapper 개체에 몇 가지 구성 옵션을 추가한다.

```java
@Test
public void givenJsonString_whenConvertToXMLUsingJacksonWithXMLDeclarationAndRoot_thenConverted() throws JsonProcessingException {
    String jsonString = "{\"name\":\"John\", \"age\":20, \"address\":{\"street\":\"Wall Street\", \"city\":\"New York\"}}";
    ObjectMapper objectMapper = new ObjectMapper();
    JsonNode jsonNode = objectMapper.readTree(jsonString);
    
    XmlMapper xmlMapper = new XmlMapper();
    xmlMapper.configure(SerializationFeature.INDENT_OUTPUT, true);
    xmlMapper.configure(ToXmlGenerator.Feature.WRITE_XML_DECLARATION, true);
    xmlMapper.configure(ToXmlGenerator.Feature.WRITE_XML_1_1, true);
    
    String xmlString = xmlMapper.writer().withRootName("root").writeValueAsString(jsonNode);
    
    Assertions.assertEquals("<?xml version='1.1' encoding='UTF-8'?>" + System.lineSeparator() +
      "<root>" + System.lineSeparator() +
      " <name>John</name>" + System.lineSeparator() +
      " <age>20</age>" + System.lineSeparator() +
      " <address>" + System.lineSeparator() +
      " <street>Wall Street</street>" + System.lineSeparator() +
      " <city>New York</city>" + System.lineSeparator() +
      " </address>" + System.lineSeparator() +
      "</root>" + System.lineSeparator(), xmlString);
}
```

XmlMapper 개체에 몇 가지 구성 옵션을 추가했다.

* `SerializationFeature.INDENT_OUTPUT`: 출력 XML 문자열을 들여쓰기하여 읽기 쉽도록 만든다.

* `ToXmlGenerator.Feature.WRITE_XML_DECLARATION`: 출력 XML 문자열에 XML 선언을 추가한다.

* `ToXmlGenerator.Feature.WRITE_XML_1_1`: XML 선언에 XML 버전 1.1을 추가한다.

* `withRootName()`: 루트 태그 이름을 ObjectNode 대신 루트로 설정한다. 

출력 XML 문자열은 이제 형식이 지정되고 XML 선언이 있으며 루트 태그는 root 이다.

## 3. Underscore-java
Underscore-java는 JSON을 XML로 변환하는 방법을 제공하는 유틸리티 라이브러리이다. 특히 작동하려면 Java 11 이상이 필요하다.

Jackson 예에서는 XML 표준에 따라 출력을 사용자 정의하기 위해 XmlMapper 개체에 몇 가지 구성 옵션을 추가해야 했다. Underscore-java는 기본적으로 XML 표준을 따르며 이러한 구성 옵션이 필요하지 않다.

### 1) Dependency
`pom.xml`에 Underscore-java 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.github.javadev</groupId>
    <artifactId>underscore-java</artifactId>
    <version>1.89</version>
</dependency>
```

### 2) Code Example
Underscore-java를 사용하여 JSON 문자열을 XML로 변환하는 테스트 예제이다.

```java
@Test
public void givenJsonString_whenConvertToXMLUsingUnderscoreJava_thenConverted() {
    String jsonString = "{\"name\":\"John\", \"age\":20}";
    String xmlString = U.jsonToXml(jsonString);
    Assertions.assertEquals("<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n" +
      "<root>\n" +
      "  <name>John</name>\n" +
      "  <age number=\"true\">20</age>\n" +
      "</root>", xmlString);
}
```

`U.jsonToXml()` 메서드를 사용하여 JSON 문자열을 XML로 변환할 수 있다.

또한 **root 요소와 선언을 XML 문자열에 추가한다.** 다른 라이브러리와 달리 출력은 가독성을 위해 기본적으로 형식이 지정된다.

문자열이 아닌 모든 필드의 경우 태그에 유형 속성을 추가한다. 예를 들어 age 요소에 number 속성을 추가한다. 이렇게 하면 필요한 경우 XML 문자열을 다시 JSON으로 구문 분석하는 것이 더 쉬워진다.

속성이 필요하지 않으면 `U.JsonToXmlMode.REMOVE_ATTRIBUTES` 옵션을 사용하여 비활성화할 수 있다.

```java
@Test
public void givenJsonString_whenConvertToXMLUsingUnderscoreJavaWithoutAttributes_thenConverted() {
    String jsonString = "{\"name\":\"John\", \"age\":20}";
    String xmlString = U.jsonToXml(jsonString, U.JsonToXmlMode.REMOVE_ATTRIBUTES);
    Assertions.assertEquals("<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n" +
      "<root>\n" +
      "  <name>John</name>\n" +
      "  <age>20</age>\n" +
      "</root>", xmlString);
}
```

number 속성은 더 이상 age 요소에 추가되지 않는다.

## [출처 및 참고]
* [https://www.baeldung.com/java-convert-json-to-xm](https://www.baeldung.com/java-convert-json-to-xm)
