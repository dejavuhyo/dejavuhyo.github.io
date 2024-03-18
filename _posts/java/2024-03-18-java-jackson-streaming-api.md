---
title: Java Jackson Streaming API
author: dejavuhyo
date: 2024-03-18 11:16:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-streaming-api, jacksonapi]
---

## 1. Maven Dependency
`pom.xml`에 [jackson-core](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind) 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.11.1</version>
</dependency>
```

## 2. JSON 쓰기
[JsonGenerator](https://fasterxml.github.io/jackson-core/javadoc/2.6/com/fasterxml/jackson/core/JsonGenerator.html) 클래스를 사용하여 JSON 콘텐츠를 OutputStream에 직접 작성할 수 있다. 먼저 해당 객체의 인스턴스를 생성해야 한다.

```java
ByteArrayOutputStream stream = new ByteArrayOutputStream();
JsonFactory jfactory = new JsonFactory();
JsonGenerator jGenerator = jfactory
  .createGenerator(stream, JsonEncoding.UTF8);
```

다음으로, 다음 구조의 JSON을 작성한다.

```json
{  
   "name":"Tom",
   "age":25,
   "address":[  
      "Poland",
      "5th avenue"
   ]
}
```

JsonGenerator의 인스턴스를 사용하여 특정 필드를 OutputStream에 직접 쓸 수 있다.

```java
jGenerator.writeStartObject();
jGenerator.writeStringField("name", "Tom");
jGenerator.writeNumberField("age", 25);
jGenerator.writeFieldName("address");
jGenerator.writeStartArray();
jGenerator.writeString("Poland");
jGenerator.writeString("5th avenue");
jGenerator.writeEndArray();
jGenerator.writeEndObject();
jGenerator.close();
```

적절한 JSON이 생성되었는지 확인하려면 JSON 객체가 포함된 String 객체를 생성하면 된다.

```java
String json = new String(stream.toByteArray(), "UTF-8");
assertEquals(
  json, 
  "{\"name\":\"Tom\",\"age\":25,\"address\":[\"Poland\",\"5th avenue\"]}");
```

## 3. JSON 파싱
JSON 문자열을 입력으로 받고 여기에서 특정 필드를 추출하려는 경우 JsonParser 클래스를 사용할 수 있다.

```java
String json
  = "{\"name\":\"Tom\",\"age\":25,\"address\":[\"Poland\",\"5th avenue\"]}";
JsonFactory jfactory = new JsonFactory();
JsonParser jParser = jfactory.createParser(json);

String parsedName = null;
Integer parsedAge = null;
List<String> addresses = new LinkedList<>();
```

입력 JSON에서 구문 분석된 이름, 구문 분석된 연령 및 주소 필드를 얻으려고 한다. 이를 달성하려면 낮은 수준의 구문 분석 논리를 처리하고 직접 구현해야 한다.

```java
while (jParser.nextToken() != JsonToken.END_OBJECT) {
    String fieldname = jParser.getCurrentName();
    if ("name".equals(fieldname)) {
        jParser.nextToken();
        parsedName = jParser.getText();
    }

    if ("age".equals(fieldname)) {
        jParser.nextToken();
        parsedAge = jParser.getIntValue();
    }

    if ("address".equals(fieldname)) {
        jParser.nextToken();
        while (jParser.nextToken() != JsonToken.END_ARRAY) {
            addresses.add(jParser.getText());
        }
    }
}
jParser.close();
```

필드 이름에 따라 이를 추출하여 적절한 필드에 할당하고 있다. 문서를 구문 분석한 후 모든 필드에 올바른 데이터가 있어야 한다.

```java
assertEquals(parsedName, "Tom");
assertEquals(parsedAge, (Integer) 25);
assertEquals(addresses, Arrays.asList("Poland", "5th avenue"));
```

## 4. JSON 부분 추출
때로는 JSON 문서를 구문 분석할 때 하나의 특정 필드에만 관심이 있는 경우가 있다.

이상적으로는 이러한 상황에서 문서의 시작 부분만 구문 분석하고 필요한 필드가 발견되면 처리를 중단할 수 있다.

입력 JSON의 age 필드에만 관심이 있다고 가정한다. 이 경우 필요한 필드가 발견되면 구문 분석을 중지하는 구문 분석 논리를 구현할 수 있다.

```java
while (jParser.nextToken() != JsonToken.END_OBJECT) {
    String fieldname = jParser.getCurrentName();

    if ("age".equals(fieldname)) {
        jParser.nextToken();
        parsedAge = jParser.getIntValue();
        return;
    }

}
jParser.close();
```

처리 후 유일한 parsedAge 필드의 값은 다음과 같다.

```java
assertNull(parsedName);
assertEquals(parsedAge, (Integer) 25);
assertTrue(addresses.isEmpty());
```

전체 문서를 읽을 필요가 없고 일부만 읽을 필요가 있기 때문에 JSON 문서의 구문 분석이 훨씬 더 빨라질 것이다.

## [출처 및 참고]
* [https://www.baeldung.com/jackson-streaming-api](https://www.baeldung.com/jackson-streaming-api)
