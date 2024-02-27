---
title: Java Jackson @JsonMerge Annotation
author: dejavuhyo
date: 2024-02-27 10:57:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-jsonmerge, jsonmerge-annotation]
---

## 1. @JsonMerge가 하는 일
가장 자주 사용되는 Jackson 기능 중 하나는 JSON을 Java 객체에 매핑하고 그 반대의 작업을 수행할 수 있는 ObjectMapper이다. ObjectMapper의 기능 중 하나는 JSON이 올바른 구조에 있다고 가정하고 객체를 읽고 JSON String의 새 데이터로 업데이트하는 것이다. `@JsonMerge`가 도입되기 전에는 POJO 및 Map을 덮어쓰는 업데이트 기능의 제한이 있었다. 이 주석을 사용하면 중첩된 POJO 및 Map 내의 속성이 업데이트에 병합된다.

@JsonMerge를 실제로 사용하는 방법이다. 먼저 키보드라는 두 개의 개체를 만든다.

```java
class Keyboard {
    String style;
    String layout;
    // Standard getters, setters and constructors
}
```

둘째, 키보드를 사용할 프로그래머이다.

```java
class ProgrammerNotAnnotated {
    String name;
    String favouriteLanguage;
    Keyboard keyboard;
    // Standard getters, setters and constructors
}
```
나중에 `@JsonMerge` 주석을 추가할 예정이다.

## 2. @JsonMerge 없이 병합
객체를 업데이트하려면 먼저 병합하려는 새 데이터를 나타내는 JSON 문자열이 필요하다.

```java
String newData = "{\"favouriteLanguage\":\"Java\",\"keyboard\":{\"style\":\"Mechanical\"}}";
```

그런 다음 새 데이터로 업데이트하려는 객체를 생성해야 한다.

```java
ProgrammerNotAnnotated programmerToUpdate = new ProgrammerNotAnnotated("John", "C++", new Keyboard("Membrane", "US"));
```

방금 정의한 문자열과 객체를 사용하여 주석 없이 어떤 일이 일어나는지 확인한다. 먼저 ObjectMapper 인스턴스를 생성하고 이를 사용하여 ObjectReader를 생성한다. ObjectReader는 더 적은 오버헤드로 ObjectMapper와 동일한 기능을 많이 사용할 수 있는 가볍고 스레드로부터 안전한 객체이다. ObjectReader 인스턴스는 만들고 구성하는데 비용이 매우 저렴하므로 직렬화/역직렬화별로 사용할 수 있다.

`ObjectMapper.readerForUpdating()`을 사용하여 ObjectReader를 생성하고 업데이트하려는 개체를 유일한 인수로 전달한다. 이는 JSON String의 새 데이터로 지정된 객체를 업데이트하는 ObjectReader 인스턴스를 반환하기 위한 팩토리 메서드이다. ObjectReader가 있으면 간단히 `readValue()`를 호출하고 새 데이터를 전달한다.

```java
@Test
void givenAnObjectAndJson_whenNotUsingJsonMerge_thenExpectNoUpdateInPOJO() throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper();
    ObjectReader objectReader = objectMapper.readerForUpdating(programmerToUpdate);
    ProgrammerNotAnnotated update = objectReader.readValue(newData);

    assert(update.getFavouriteLanguage()).equals("Java");
    assertNull(update.getKeyboard()
      .getLayout());
}
```

그런 다음 업데이트를 인쇄하여 최종 결과를 명확하게 확인할 수 있다.

```json
{name='John', favouriteLanguage='Java', keyboard=Keyboard{style='Mechanical', layout='null'}}
```

programmerToUpdate가 최상위 업데이트를 받았다는 테스트 주장과 JSON을 보면 그가 가장 좋아하는 언어는 이제 Java이다. 그러나 중첩된 Keyboard 개체를 완전히 덮어썼으며 새 데이터에 스타일만 포함되어 있어도 레이아웃 속성이 손실되었다. 키보드와 같은 POJO를 병합하는 기능은 `@JsonMerge` 주석의 주요 이점 중 하나이다.

## 3. @JsonMerge로 병합
`@JsonMerge` 주석을 사용하여 새 Programmer 개체를 만든다.

```java
class ProgrammerAnnotated {
    String name;
    String favouriteLanguage;
    @JsonMerge
    Keyboard keyboard;
    // Standard getters, setters and constructors
}
```

해당 객체를 위와 같은 방식으로 사용하면 다른 결과를 얻는다.

```java
@Test
void givenAnObjectAndJson_whenUsingJsonMerge_thenExpectUpdateInPOJO() throws JsonProcessingException {
    String newData = "{\"favouriteLanguage\":\"Java\",\"keyboard\":{\"style\":\"Mechanical\"}}";
    ProgrammerAnnotated programmerToUpdate = new ProgrammerAnnotated("John", "C++", new Keyboard("Membrane", "US"));

    ObjectMapper objectMapper = new ObjectMapper();
    ProgrammerAnnotated update = objectMapper.readerForUpdating(programmerToUpdate).readValue(newData);

    assert(update.getFavouriteLanguage()).equals("Java");
    // Only works with annotation
    assert(update.getKeyboard().getLayout()).equals("US");
}
```

마지막으로 업데이트를 인쇄하여 이번에는 중첩된 Keyboard POJO를 업데이트했는지 확인할 수 있다.

```json
{name='John', favouriteLanguage='Java', keyboard=Keyboard{style='Mechanical', layout='US'}}
```

주석의 동작은 여기에서 명확하게 볼 수 있다. 중첩된 개체의 수신 필드는 기존 필드를 덮어쓴다. 새 데이터에 일치하는 항목이 없는 필드는 그대로 유지된다.

## 4. @JsonMerge를 사용 하여 맵 병합
맵을 병합하는 과정은 우리가 이미 본 것과 매우 유사하다. 예제에서 사용할 수 있는 맵을 사용하여 객체를 생성한다.

```java
class ObjectWithMap {
    String name;
    @JsonMerge
    Map<String, String> stringPairs;
    // Standard getters, setters and constructors
}
```

그런 다음 객체를 업데이트할 맵이 포함된 시작 JSON 문자열을 생성한다.

```java
String newData = "{\"stringPairs\":{\"field1\":\"value1\",\"field2\":\"value2\"}}";
```

마지막으로 업데이트하려는 ObjectWithMap 인스턴스가 필요하다.

```java
Map<String, String> map = new HashMap<>();
map.put("field3", "value3");
ObjectWithMap objectToUpdateWith = new ObjectWithMap("James", map);
```

이제 객체를 업데이트하기 위해 이전에 사용한 것과 동일한 프로세스를 사용할 수 있다.

```java
@Test
void givenAnObjectWithAMap_whenUsingJsonMerge_thenExpectAllFieldsInMap() throws JsonProcessingException {
    ObjectMapper objectMapper = new ObjectMapper();
    ObjectWithMap update = objectMapper.readerForUpdating(objectToUpdateWith).readValue(newData);

    assertTrue(update.getStringPairs().containsKey("field1"));
    assertTrue(update.getStringPairs().containsKey("field2"));
    assertTrue(update.getStringPairs().containsKey("field3"));
}
```

최종 결과를 확인하기 위해 업데이트를 다시 인쇄하면 다음과 같다.

```json
{name='James', something={field1=value1, field3=value3, field2=value2}}
```

테스트와 결과물을 통해 주석을 사용하면 Map에 세 쌍이 모두 존재한다는 것을 알 수 있다. 주석이 없으면 새 데이터의 쌍만 갖게 된다.

## [출처 및 참고]
* [https://www.baeldung.com/java-jsonmerge-annotation-jackson](https://www.baeldung.com/java-jsonmerge-annotation-jackson)
