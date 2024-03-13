---
title: Java JsonNode로 JSON 모든 키 가져오기
author: dejavuhyo
date: 2024-03-13 13:30:00 +0900
categories: [Language, Java]
tags: [java-jackson, jsonnode-keys, jsonnode-키]
---

## 1. Jackson 라이브러리
Jackson 라이브러리는 트리 모델을 사용하여 JSON 데이터를 나타낸다. 트리 모델은 계층적 데이터와 상호 작용하는 효율적인 방법을 제공한다.

JSON 개체는 트리 모델의 노드로 표시된다. 이렇게 하면 JSON 콘텐츠에 대한 CRUD 작업을 더 쉽게 수행할 수 있다.

### 1) ObjectMapper
ObjectMapper 클래스 메서드를 사용하여 JSON 콘텐츠를 읽는다. `ObjectMapper.readTree()` 메서드는 JSON을 역직렬화하고 JsonNode 인스턴스 트리를 구축한다. JSON 소스를 입력으로 사용하고 생성된 트리 모델의 루트 노드를 반환한다. 그런 다음 루트 노드를 사용하여 전체 JSON 트리를 탐색할 수 있다.

트리 모델은 일반 Java 객체만 읽는 것으로 제한되지 않는다. JSON 필드와 트리 모델 사이에는 일대일 매핑이 있다. 따라서 POJO 여부에 관계없이 각 객체는 노드로 표시될 수 있다. 따라서 JSON 콘텐츠를 일반 노드로 표현하는 유연한 접근 방식을 선호한다.

### 2) JsonNode
JsonNode 클래스는 JSON 트리 모델의 노드를 나타낸다. `Array`, `Binary`, `Boolean`, `Missing`, `Null`, `Number`, `Object`, `POJO`, `String` 데이터 유형으로 JSON 데이터를 표현할 수 있다. 이러한 데이터 유형은 JsonNodeType enum에 정의되어 있다.

## 2. JSON에서 키 가져오기
다음 JSON을 입력으로 사용한다.

```json
{
   "Name":"Craig",
   "Age":10,
   "BookInterests":[
      {
         "Book":"The Kite Runner",
         "Author":"Khaled Hosseini"
      },
      {
         "Book":"Harry Potter",
         "Author":"J. K. Rowling"
      }
   ],
   "FoodInterests":{
      "Breakfast":[
         {
            "Bread":"Whole wheat",
            "Beverage":"Fruit juice"
         },
         {
            "Sandwich":"Vegetable Sandwich",
            "Beverage":"Coffee"
         }
      ]
   }
}
```

String 객체를 입력으로 사용하고 있지만 `File`, `byte[]`, `URL`, `InputStream`, `JsonParser` 등과 같은 다양한 소스에서 JSON 콘텐츠를 읽을 수 있다.

### 1) fieldName 사용
JsonNode 인스턴스에서 `fieldNames()` 메서드를 사용하여 중첩된 필드 이름을 가져올 수 있다. 직접 중첩된 필드의 이름만 반환 한다.

간단한 예이다.

```java
public List<String> getKeysInJsonUsingJsonNodeFieldNames(String json, ObjectMapper mapper) throws JsonMappingException, JsonProcessingException {

    List<String> keys = new ArrayList<>();
    JsonNode jsonNode = mapper.readTree(json);
    Iterator<String> iterator = jsonNode.fieldNames();
    iterator.forEachRemaining(e -> keys.add(e));
    return keys;
}
```

우리는 다음과 같은 키를 얻는다.

```text
[Name, Age, BookInterests, FoodInterests]
```

내부 중첩 노드를 모두 가져오려면 각 수준의 노드에서 `fieldNames()` 메서드를 호출해야 한다.

```java
public List<String> getAllKeysInJsonUsingJsonNodeFieldNames(String json, ObjectMapper mapper) throws JsonMappingException, JsonProcessingException {

    List<String> keys = new ArrayList<>();
    JsonNode jsonNode = mapper.readTree(json);
    getAllKeysUsingJsonNodeFieldNames(jsonNode, keys);
    return keys;
}
```

```java
private void getAllKeysUsingJsonNodeFields(JsonNode jsonNode, List<String> keys) {

    if (jsonNode.isObject()) {
        Iterator<Entry<String, JsonNode>> fields = jsonNode.fields();
        fields.forEachRemaining(field -> {
            keys.add(field.getKey());
            getAllKeysUsingJsonNodeFieldNames((JsonNode) field.getValue(), keys);
        });
    } else if (jsonNode.isArray()) {
        ArrayNode arrayField = (ArrayNode) jsonNode;
        arrayField.forEach(node -> {
            getAllKeysUsingJsonNodeFieldNames(node, keys);
        });
    }
}
```

먼저 JSON 값이 객체인지 배열인지 확인한다. 그렇다면 값 개체도 탐색하여 내부 노드를 가져온다.

결과적으로 JSON에 있는 모든 키 이름을 얻게 된다.

```text
[Name, Age, BookInterests, Book, Author,
  Book, Author, FoodInterests, Breakfast, Bread, Beverage, Sandwich, Beverage]
```

위의 예에서는 JsonNode 클래스의 `fields()` 메서드를 사용하여 필드 이름뿐 아니라 필드 객체를 가져올 수도 있다.

```java
public List<String> getAllKeysInJsonUsingJsonNodeFields(String json, ObjectMapper mapper) throws JsonMappingException, JsonProcessingException {

    List<String> keys = new ArrayList<>();
    JsonNode jsonNode = mapper.readTree(json);
    getAllKeysUsingJsonNodeFields(jsonNode, keys);
    return keys;
}

private void getAllKeysUsingJsonNodeFields(JsonNode jsonNode, List<String> keys) {

    if (jsonNode.isObject()) {
        Iterator<Entry<String, JsonNode>> fields = jsonNode.fields();
        fields.forEachRemaining(field -> {
            keys.add(field.getKey());
            getAllKeysUsingJsonNodeFieldNames((JsonNode) field.getValue(), keys);
        });
    } else if (jsonNode.isArray()) {
        ArrayNode arrayField = (ArrayNode) jsonNode;
        arrayField.forEach(node -> {
            getAllKeysUsingJsonNodeFieldNames(node, keys);
        });
    }
}
```

### 2) JsonParser 사용
낮은 수준의 JSON 구문 분석을 위해 JsonParser 클래스를 사용할 수도 있다. JsonParser는 지정된 JSON 콘텐츠에서 반복 가능한 토큰 시퀀스를 생성한다. 토큰 유형은 아래와 같이 JsonToken 클래스에서 열거형으로 지정된다.

* NOT_AVAILABLE

* START_OBJECT

* END_OBJECT

* START_ARRAY

* FIELD_NAME

* VALUE_EMBEDDED_OBJECT

* VALUE_STRING

* VALUE_NUMBER_INT

* VALUE_NUMBER_FLOAT

* VALUE_TRUE

* VALUE_FALSE

* VALUE_NULL

JsonParser를 사용하여 반복하는 동안 토큰 유형을 확인하고 필요한 작업을 수행할 수 있다. 예제 JSON 문자열의 모든 필드 이름을 가져온다.

```java
public List<String> getKeysInJsonUsingJsonParser(String json, ObjectMapper mapper) throws IOException {

    List<String> keys = new ArrayList<>();
    JsonNode jsonNode = mapper.readTree(json);
    JsonParser jsonParser = jsonNode.traverse();
    while (!jsonParser.isClosed()) {
        if (jsonParser.nextToken() == JsonToken.FIELD_NAME) {
            keys.add((jsonParser.getCurrentName()));
        }
    }
    return keys;
}
```

여기서는 JsonNode 클래스의 `traverse()` 메서드를 사용하여 JsonParser 개체를 가져왔다. 마찬가지로 JsonFactory를 사용하여 JsonParser 객체를 생성할 수도 있다.

```java
public List<String> getKeysInJsonUsingJsonParser(String json) throws JsonParseException, IOException {

    List<String> keys = new ArrayList<>();
    JsonFactory factory = new JsonFactory();
    JsonParser jsonParser = factory.createParser(json);
    while (!jsonParser.isClosed()) {
        if (jsonParser.nextToken() == JsonToken.FIELD_NAME) {
            keys.add((jsonParser.getCurrentName()));
        }
    }
    return keys;
}
```

결과적으로 예제 JSON 콘텐츠에서 추출된 모든 키 이름을 얻게 된다.

```text
[Name, Age, BookInterests, Book, Author,
  Book, Author, FoodInterests, Breakfast, Bread, Beverage, Sandwich, Beverage]
```

### 3) Map 사용
ObjectMapper 클래스의 `readValue()` 메서드를 사용하여 JSON 콘텐츠를 Map으로 역직렬화할 수 있다. 결과적으로 Map 객체를 반복하면서 JSON 요소를 추출할 수 있다. 다음 접근 방식을 사용하여 예제 JSON에서 모든 키를 가져온다.

```java
public List<String> getKeysInJsonUsingMaps(String json, ObjectMapper mapper) throws JsonMappingException, JsonProcessingException {
    List<String> keys = new ArrayList<>();
    Map<String, Object> jsonElements = mapper.readValue(json, new TypeReference<Map<String, Object>>() {
    });
    getAllKeys(jsonElements, keys);
    return keys;
}

private void getAllKeys(Map<String, Object> jsonElements, List<String> keys) {

    jsonElements.entrySet()
        .forEach(entry -> {
            keys.add(entry.getKey());
            if (entry.getValue() instanceof Map) {
                Map<String, Object> map = (Map<String, Object>) entry.getValue();
                getAllKeys(map, keys);
            } else if (entry.getValue() instanceof List) {
                List<?> list = (List<?>) entry.getValue();
                list.forEach(listEntry -> {
                    if (listEntry instanceof Map) {
                        Map<String, Object> map = (Map<String, Object>) listEntry;
                        getAllKeys(map, keys);
                    }
                });
            }
        });
}
```

또한 이 경우 최상위 노드를 얻은 후 객체(맵) 또는 배열로 값을 갖는 JSON 객체를 탐색하여 중첩된 노드를 얻는다.

## [출처 및 참고]
* [https://www.baeldung.com/java-jsonnode-get-keys](https://www.baeldung.com/java-jsonnode-get-keys)
