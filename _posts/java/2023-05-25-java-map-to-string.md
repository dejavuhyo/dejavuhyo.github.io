---
title: Java Map을 String으로 변환
author: dejavuhyo
date: 2023-05-25 09:20:00 +0900
categories: [Language, Java]
tags: [java-convert, map-string, 맵-스트링, 자바-변환]
---

## 1. 기본 맵 예시
예에서 동일한 Map 구현을 사용한다.

```java
Map<Integer, String> wordsByKey = new HashMap<>();
wordsByKey.put(1, "one");
wordsByKey.put(2, "two");
wordsByKey.put(3, "three");
wordsByKey.put(4, "four");
```

## 2. Map을 String으로 변환

### 1) 반복을 이용
맵의 모든 키를 반복 하고 각 키에 대해 결과 StringBuilder 개체에 key-value 조합을 추가한다.

서식 지정을 위해 결과를 중괄호로 묶을 수 있다.

```java
public String convertWithIteration(Map<Integer, ?> map) {
    StringBuilder mapAsString = new StringBuilder("{");
    for (Integer key : map.keySet()) {
        mapAsString.append(key + "=" + map.get(key) + ", ");
    }
    mapAsString.delete(mapAsString.length()-2, mapAsString.length()).append("}");
    return mapAsString.toString();
}
```

맵을 올바르게 변환했는지 확인하기 위해 테스트를 실행한다.

```java
@Test
public void givenMap_WhenUsingIteration_ThenResultingStringIsCorrect() {
    String mapAsString = MapToString.convertWithIteration(wordsByKey);
    Assert.assertEquals("{1=one, 2=two, 3=three, 4=four}", mapAsString);
}
```

### 2) Java Streams 사용
스트림을 사용하여 변환을 수행하려면 먼저 사용 가능한 맵 키에서 스트림을 만들어야 한다.

각 키를 사람이 읽을 수 있는 String에 매핑한다.

마지막으로 이러한 값을 결합하고 편의를 위해 `Collectors.joining()` 메서드를 사용하여 몇 가지 서식 지정 규칙을 추가한다.

```java
public String convertWithStream(Map<Integer, ?> map) {
    String mapAsString = map.keySet().stream()
      .map(key -> key + "=" + map.get(key))
      .collect(Collectors.joining(", ", "{", "}"));
    return mapAsString;
}
```

### 3) Guava 사용
프로젝트에 구아바를 추가하고 한 줄의 코드로 변환을 수행하는 방법이다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.0.1-jre</version>
</dependency>
```

Guava의 Joiner 클래스를 사용하여 변환을 수행하려면 서로 다른 맵 항목 사이의 구분 기호와 키와 값 사이의 구분 기호를 정의해야 한다.

```java
public String convertWithGuava(Map<Integer, ?> map) {
    return Joiner.on(",").withKeyValueSeparator("=").join(map);
}
```

### 4) Apache Commons 사용
Apache Commons를 사용하려면 먼저 다음 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.2</version>
</dependency>
```

조인은 매우 간단하다. `StringUtils.join` 메서드를 호출하기만 하면 된다.

```java
public String convertWithApache(Map map) {
    return StringUtils.join(map);
}
```

한 가지 특별한 방법은 Apache Commons에서 사용할 수 있는 debugPrint 방법 입니다. 디버깅 목적으로 매우 유용하다.

```java
MapUtils.debugPrint(System.out, "Map as String", wordsByKey);
```

디버그 텍스트가 콘솔에 기록된다.

```text
Map as String = 
{
    1 = one java.lang.String
    2 = two java.lang.String
    3 = three java.lang.String
    4 = four java.lang.String
} java.util.HashMap
```

## 3. String을 Map으로 변환

### 1) Streams 사용
String에서 맵으로 변환을 수행하기 위해 분할할 위치와 키와 값을 추출하는 방법이다.

```java
public Map<String, String> convertWithStream(String mapAsString) {
    Map<String, String> map = Arrays.stream(mapAsString.split(","))
      .map(entry -> entry.split("="))
      .collect(Collectors.toMap(entry -> entry[0], entry -> entry[1]));
    return map;
}
```

### 2) Guava 사용
더 간단한 방법은 Guava를 사용하는 것이다.

```java
public Map<String, String> convertWithGuava(String mapAsString) {
    return Splitter.on(',').withKeyValueSeparator('=').split(mapAsString);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-map-to-string-conversion](https://www.baeldung.com/java-map-to-string-conversion)
