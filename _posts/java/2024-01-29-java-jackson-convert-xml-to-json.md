---
title: Java Jackson을 사용하여 XML을 JSON로 변환
author: dejavuhyo
date: 2024-01-29 14:05:00 +0900
categories: [Language, Java]
tags: [spring-response-header, response-header, spring-response, spring-header, 응답-헤더, 스프링-헤더]
---

## 1. Jackson이란
Jackson을 사용하여 JSON을 세 가지 다른 방식으로 구문 분석한다.

* 첫 번째이자 가장 일반적인 것은 [ObjectMapper](https://fasterxml.github.io/jackson-databind/javadoc/2.9/com/fasterxml/jackson/databind/ObjectMapper.html)를 사용한 데이터 바인딩이다.

* 두 번째는 [TreeTraversingParser](http://fasterxml.github.io/jackson-databind/javadoc/2.9/com/fasterxml/jackson/databind/node/TreeTraversingParser.html) 및 [JsonNode](http://fasterxml.github.io/jackson-databind/javadoc/2.9/com/fasterxml/jackson/databind/JsonNode.html)를 사용하여 트리 데이터 구조에 매핑하는 것이다.

* 세 번째는 [JsonParser](https://fasterxml.github.io/jackson-core/javadoc/2.9/com/fasterxml/jackson/core/JsonParser.html) 및 [JsonGenerator](https://fasterxml.github.io/jackson-core/javadoc/2.9/com/fasterxml/jackson/core/JsonGenerator.html?is-external=true)를 사용하여 토큰별로 트리 데이터 구조를 스트리밍하는 것이다.

Jackson은 XML 데이터에 대해 처음 두 가지도 지원한다. 따라서 Jackson이 한 형식에서 다른 형식으로 변환하는 방법을 확인한다.

## 2. Dependencies
`pom.xml`에 [jackson-databind](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind) 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.13.3</version>
</dependency>
```

이 라이브러리를 사용하면 데이터 바인딩 API를 사용할 수 있다.

두 번째는 Jackson의 XML 지원을 추가하는 [jackson-dataformat-xml](https://mvnrepository.com/artifact/com.fasterxml.jackson.dataformat/jackson-dataformat-xml) 이다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.13.3</version>
</dependency>
```

## 3. 데이터 바인딩
간단히 말해서 데이터 바인딩은 직렬화된 데이터를 Java 객체에 직접 매핑하려는 경우이다.

이를 탐색하기 위해 Flower 및 Color 속성을 사용하여 XML을 정의한다.

```xml
<Flower>
    <name>Poppy</name>
    <color>RED</color>
    <petals>9</petals>
</Flower>
```

이는 다음 Java 표기법과 유사하다.

```java
public class Flower {
    private String name;
    private Color color;
    private Integer petals;
    // getters and setters
}

public enum Color { PINK, BLUE, YELLOW, RED; }
```

첫 번째 단계는 XML을 Flower 인스턴스로 구문 분석하는 것이다. 이를 위해 Jackson의 ObjectMapper에 해당하는 XML인 XmlMapper 인스턴스를 만들고 readValue 메서드를 사용한다.

```java
XmlMapper xmlMapper = new XmlMapper();
Flower poppy = xmlMapper.readValue(xml, Flower.class);
```

Flower 인스턴스가 있으면 익숙한 ObjectMapper를 사용하여 JSON으로 작성하려고 한다.

```java
ObjectMapper mapper = new ObjectMapper();
String json = mapper.writeValueAsString(poppy);
```

결과 JSON이다.

```json
{
    "name":"Poppy",
    "color":"RED",
    "petals":9
}
```

## 4. 트리 순회
때로는 중간 클래스를 유지하고 싶지 않거나 구조의 일부만 변환하려는 경우처럼 트리 구조를 직접 살펴보면 더 많은 유연성을 제공할 수 있다.

하지만 여기에는 몇 가지 장단점이 있다.

첫 번째 단계는 데이터 바인딩을 사용할 때의 첫 번째 단계와 유사하다. 하지만 이번에는 readTree 메소드를 사용한다.

```java
XmlMapper xmlMapper = new XmlMapper();
JsonNode node = xmlMapper.readTree(xml.getBytes());
```

이 작업을 수행하면 예상대로 name, color 그리고 petals 3개의 하위 항목이 있는 JsonNode가 생성된다.

그런 다음 ObjectMapper를 사용하여 대신 JsonNode를 보낼 수 있다.

```java
ObjectMapper jsonMapper = new ObjectMapper();
String json = jsonMapper.writeValueAsString(node);
```

결과는 마지막 예와 약간 다르다.

```json
{
    "name":"Poppy",
    "color":"RED",
    "petals":"9"
}
```

자세히 살펴보면 petals 속성이 숫자 대신 문자열로 직렬화되어 있음을 알 수 있다. 이는 readTree가 명시적인 정의 없이는 데이터 유형을 유추하지 않기 때문이다.

### 1) 제한사항
Jackson의 XML 트리 탐색 지원에는 다음과 같은 특정 제한 사항이 있다.

* Jackson은 객체와 배열을 구별할 수 없다. XML에는 객체 목록과 객체를 구별하는 기본 구조가 없기 때문에 Jackson은 반복되는 요소를 단일 값으로 간단히 조합한다.

* Jackson은 각 XML 요소를 JSON 노드에 매핑하려고 하기 때문에 혼합 콘텐츠를 지원하지 않는다.

이러한 이유로 [공식 Jackson 문서에서는 XML을 구문 분석하기 위해 Tree 모델을 사용하지 말 것](https://github.com/FasterXML/jackson-dataformat-xml#known-limitations)을 권장한다.

## 5. 메모리 제약
이 두 가지 모두 변환을 수행하려면 전체 XML이 한 번에 메모리에 있어야 한다는 단점이 있다. Jackson이 트리 구조를 토큰으로 스트리밍하는 것을 지원할 때까지 이 제약 조건을 고수하거나 XMLStreamReader와 같은 것을 사용하여 자체적으로 롤링하는 방법이 있다.

## [출처 및 참고]
* [https://www.baeldung.com/jackson-convert-xml-json](https://www.baeldung.com/jackson-convert-xml-json)
