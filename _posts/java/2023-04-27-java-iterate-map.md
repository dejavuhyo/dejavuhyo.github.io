---
title: Java 맵 반복
author: dejavuhyo
date: 2023-04-27 09:20:00 +0900
categories: [Language, Java]
tags: [java-iterate-map, iterate-map, 자바-맵-반복, 맵-반복]
---

## 1. Map의 entrySet(), keySet(), values() 메서드 설명
세 가지 방법을 사용하여 맵을 반복하기 전에 이러한 방법이 수행하는 작업에 대한 설명이다.

* [entrySet()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html#entrySet()) - 요소가 Map.Entry 클래스에 있는 맵의 컬렉션 뷰를 반환한다. `entry.getKey()` 메서드는 키를 반환하고 `entry.getValue()`는 해당 값을 반환한다.

* [keySet()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html#keySet()) - 이 맵에 포함된 모든 키를 Set으로 반환한다.

* [values()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html#values()) - 이 맵에 포함된 모든 값을 Set으로 반환한다.

## 2. for 루프 사용

### 1) entrySet() 사용
`entrySet()`을 사용하여 맵을 반복하는 방법이다.

```java
public void iterateUsingEntrySet(Map<String, Integer> map) {
    for (Map.Entry<String, Integer> entry : map.entrySet()) {
        System.out.println(entry.getKey() + ":" + entry.getValue());
    }
}
```

맵에서 항목 집합을 추출한 다음 고전적인 for-each 접근 방식을 사용하여 항목을 반복한다.

### 2) keySet() 사용
`keySet()` 메서드를 사용하여 맵의 모든 키를 가져온 다음 각 키별로 맵을 반복할 수 있다.

```java
public void iterateUsingKeySetAndForeach(Map<String, Integer> map) {
    for (String key : map.keySet()) {
        System.out.println(key + ":" + map.get(key));
    }
}
```

### 3) values()를 사용하여 값 반복
어떤 키가 연관되어 있는지에 관계없이 맵의 값에만 관심이 있는 경우가 있다. 이 경우 `values()`가 최선의 선택이다.

```java
public void iterateValues(Map<String, Integer> map) {
    for (Integer value : map.values()) {
        System.out.println(value);
    }
}
```

## 3. Iterator
반복을 수행하는 또 다른 방법은 Iterator를 사용하는 것이다. 그리고 메소드가 Iterator 객체와 어떻게 작동하는지 확인한다.

### 1) Iterator와 entrySet()
Iterator와 `entrySet()`을 사용하여 맵을 반복한다.

```java
public void iterateUsingIteratorAndEntry(Map<String, Integer> map) {
    Iterator<Map.Entry<String, Integer>> iterator = map.entrySet().iterator();
    while (iterator.hasNext()) {
        Map.Entry<String, Integer> entry = iterator.next();
        System.out.println(entry.getKey() + ":" + entry.getValue());
    }
}
```

`entrySet()`에 의해 반환된 Set의 `iterator()` API를 사용하여 Iterator 인스턴스를 얻는 방법이다. 그런 다음 `iterator.next()`를 사용하여 Iterator를 반복한다.

### 2) Iterator와 keySet()
Iterator와 `keySet()`을 사용하여 맵을 반복할 수 있다.

```java
public void iterateUsingIteratorAndKeySet(Map<String, Integer> map) {
    Iterator<String> iterator = map.keySet().iterator();
    while (iterator.hasNext()) {
        String key = iterator.next();
        System.out.println(key + ":" + map.get(key));
    }
}
```

### 3) Iterator와 values()
Iterator와 `values()` 메서드를 사용하여 맵의 값을 탐색할 수도 있다.

```java
public void iterateUsingIteratorAndValues(Map<String, Integer> map) {
    Iterator<Integer> iterator = map.values().iterator();
    while (iterator.hasNext()) {
        Integer value = iterator.next();
        System.out.println("value :" + value);
    }
}
```

## 4. 람다와 스트림 API 사용
자바 버전 8부터 Java는 Stream API와 람다를 도입했다. 이러한 기술을 사용하여 맵을 반복하는 방법이다.

### 1) forEach() 및 Lambda 사용
Java 8의 다른 대부분과 마찬가지로 이것은 대안보다 훨씬 간단하다. `forEach()` 메소드를 사용한다.

```java
public void iterateUsingLambda(Map<String, Integer> map) {
    map.forEach((k, v) -> System.out.println((k + ":" + v)));
}
```

이 경우 맵을 항목 집합으로 변환할 필요가 없다.

또한 키부터 시작하여 맵을 반복할 수 있다.

```java
public void iterateByKeysUsingLambda(Map<String, Integer> map) {
    map.keySet().foreach(k -> System.out.println((k + ":" + map.get(k))));
}
```

유사하게 `values()` 메서드와 동일한 기술을 사용할 수 있다.

```java
public void iterateValuesUsingLambda(Map<String, Integer> map) {
    map.values().forEach(v -> System.out.println(("value: " + v)));
}
```

### 2) 스트림 API 사용
Stream API는 Java 8의 중요한 기능 중 하나이다. 이 기능을 사용하여 맵을 반복할 수도 있다.

Stream API는 추가 Stream 처리를 계획할 때 사용해야 한다. 그렇지 않으면 앞에서 설명한 것처럼 단순한 `forEach()`일 뿐이다.

Stream API가 어떻게 작동하는지 보기 위해 `entrySet()`를 예로 들어 본다.

```java
public void iterateUsingStreamAPI(Map<String, Integer> map) {
    map.entrySet().stream()
      // ... some other Stream processings
      .forEach(e -> System.out.println(e.getKey() + ":" + e.getValue()));
}
```

`keySet()` 및 `values()` 메서드와 함께 Stream API를 사용하는 것은 위의 예와 매우 유사하다.

## [출처 및 참고]
* [https://www.baeldung.com/java-iterate-map](https://www.baeldung.com/java-iterate-map)
