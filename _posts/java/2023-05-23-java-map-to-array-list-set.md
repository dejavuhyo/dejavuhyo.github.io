---
title: Java Map을 Array, List 또는 Set 변환
author: dejavuhyo
date: 2023-05-23 11:05:00 +0900
categories: [Language, Java]
tags: [java-convert, map-array, map-list, map-set, 맵-어레이, 맵-리스트, 맵-셋, 자바-변환]
---

## 1. Map to Array
일반 java를 사용하여 Map의 값을 배열로 변환하는 방법이다.

```java
@Test
public void givenUsingCoreJava_whenMapValuesConvertedToArray_thenCorrect() {
    Map<Integer, String> sourceMap = createMap();

    Collection<String> values = sourceMap.values();
    String[] targetArray = values.toArray(new String[0]);
}
```

`toArray (new T[0])`는 `toArray(new T[size])` 보다 메서드를 사용하는 기본 방법이다. `Aleksey Shipilëv`가 자신의 [블로그 게시물](https://shipilev.net/blog/2016/arrays-wisdom-ancients/#_conclusion)에서 증명했듯이 더 빠르고 안전하며 깨끗하다.

## 2. Map to List

### 1) 일반 Java 사용
일반 Java를 사용하여 Map 값을 List로 변환한다.

```java
@Test
public void givenUsingCoreJava_whenMapValuesConvertedToList_thenCorrect() {
    Map<Integer, String> sourceMap = createMap();

    List<String> targetList = new ArrayList<>(sourceMap.values());
}
```

### 2) 구아바 사용

```java
@Test
public void givenUsingGuava_whenMapValuesConvertedToList_thenCorrect() {
    Map<Integer, String> sourceMap = createMap();

    List<String> targetList = Lists.newArrayList(sourceMap.values());
}
```

## 3. Map to Set
일반 Java를 사용하여 Map의 값을 Set로 변환한다.

```java
@Test
public void givenUsingCoreJava_whenMapValuesConvertedToS_thenCorrect() {
    Map<Integer, String> sourceMap = createMap();

    Set<String> targetSet = new HashSet<>(sourceMap.values());
}
```

## [출처 및 참고]
* [https://www.baeldung.com/convert-map-values-to-array-list-set](https://www.baeldung.com/convert-map-values-to-array-list-set)
