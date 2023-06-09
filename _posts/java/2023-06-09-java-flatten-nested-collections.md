---
title: Java 중첩된 컬렉션 병합
author: dejavuhyo
date: 2023-06-09 09:25:00 +0900
categories: [Language, Java]
tags: [java-nested-collections, flatten-nested-collections, nested-collections, 자바-컬렉션-병합, 중첩-컬렉션-병합]
---

## 1. 중첩 컬렉션의 예
String 유형의 목록이 있다고 가정한다.

```java
List<List<String>> nestedList = asList(
  asList("one:one"), 
  asList("two:one", "two:two", "two:three"), 
  asList("three:one", "three:two", "three:three", "three:four"));
```

## 2. forEach로 List 병합
이 중첩된 컬렉션을 문자열 목록으로 병합하기 위해 Java 8 메서드 참조와 함께 forEach를 사용할 수 있다.

```java
public <T> List<T> flattenListOfListsImperatively(
    List<List<T>> nestedList) {
    List<T> ls = new ArrayList<>();
    nestedList.forEach(ls::addAll);
    return ls;
}
```

실제 사용 방법은 다음과 같다.

```java
@Test
public void givenNestedList_thenFlattenImperatively() {
    List<String> ls = flattenListOfListsImperatively(nestedList);
    
    assertNotNull(ls);
    assertTrue(ls.size() == 8);
    assertThat(ls, IsIterableContainingInOrder.contains(
      "one:one",
      "two:one", "two:two", "two:three", "three:one",
      "three:two", "three:three", "three:four"));
}
```

## 3. flatMap으로 List 병합
Stream API의 flatMap 메서드를 활용하여 중첩 목록을 평면화할 수도 있다.

이를 통해 중첩된 Stream 구조를 평면화 하고 결국 모든 요소를 특정 컬렉션으로 수집할 수 있다.

```java
public <T> List<T> flattenListOfListsStream(List<List<T>> list) {
    return list.stream()
      .flatMap(Collection::stream)
      .collect(Collectors.toList());
}
```

사용 방법은 다음과 같다.

```java
@Test
public void givenNestedList_thenFlattenFunctionally() {
    List<String> ls = flattenListOfListsStream(nestedList);
    
    assertNotNull(ls);
    assertTrue(ls.size() == 8);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-flatten-nested-collections](https://www.baeldung.com/java-flatten-nested-collections)
