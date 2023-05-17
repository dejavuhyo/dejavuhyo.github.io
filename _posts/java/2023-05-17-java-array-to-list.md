---
title: Java Array에서 List 변환
author: dejavuhyo
date: 2023-05-17 09:50:00 +0900
categories: [Language, Java]
tags: [java-convert, array-list, 어레이-리스트, 자바-변환]
---

## 1. List를 Array로 변환

### 1) 일반 자바 사용
일반 Java를 사용하여 List에서 Array로 변환한다.

```java
@Test
public void givenUsingCoreJava_whenListConvertedToArray_thenCorrect() {
    List<Integer> sourceList = Arrays.asList(0, 1, 2, 3, 4, 5);
    Integer[] targetArray = sourceList.toArray(new Integer[0]);
}
```

메서드를 사용하는 데 선호되는 방법은 `toArray(new T[0])` 대 `toArray(new T[size])`이다. `Aleksey Shipilëv`가 자신의 [블로그 게시물](https://shipilev.net/blog/2016/arrays-wisdom-ancients/#_conclusion)에서 증명했듯이 더 빠르고 안전하며 깨끗해 보인다.

### 2) 구아바 사용
동일한 변환을 위해 Guava API를 사용한다.

```java
@Test
public void givenUsingGuava_whenListConvertedToArray_thenCorrect() {
    List<Integer> sourceList = Lists.newArrayList(0, 1, 2, 3, 4, 5);
    int[] targetArray = Ints.toArray(sourceList);
}
```

## 2. Array를 List로 변환

### 1) 일반 자바 사용
Array를 List로 변환하기 위한 일반 Java 솔루션이다.

```java
@Test
public void givenUsingCoreJava_whenArrayConvertedToList_thenCorrect() {
    Integer[] sourceArray = { 0, 1, 2, 3, 4, 5 };
    List<Integer> targetList = Arrays.asList(sourceArray);
}
```

이것은 여전히 배열에 의해 지원되는 고정 크기 목록이다. 표준 ArrayList가 필요한 경우 간단하게 인스턴스화할 수 있다.

```java
List<Integer> targetList = new ArrayList<Integer>(Arrays.asList(sourceArray));
```

### 2) 구아바 사용
동일한 변환을 위해 Guava API를 사용한다.

```java
@Test
public void givenUsingGuava_whenArrayConvertedToList_thenCorrect() {
    Integer[] sourceArray = { 0, 1, 2, 3, 4, 5 };
    List<Integer> targetList = Lists.newArrayList(sourceArray);
}
```

## 3. Commons Collections 사용
[Apache Commons Collections](https://commons.apache.org/proper/commons-collections/javadocs/) `CollectionUtils.addAll` API를 사용하여 빈 목록의 배열 요소를 채운다.

```java
@Test 
public void givenUsingCommonsCollections_whenArrayConvertedToList_thenCorrect() { 
    Integer[] sourceArray = { 0, 1, 2, 3, 4, 5 }; 
    List<Integer> targetList = new ArrayList<>(6); 
    CollectionUtils.addAll(targetList, sourceArray); 
}
```

## [출처 및 참고]
* [https://www.baeldung.com/convert-array-to-list-and-list-to-array](https://www.baeldung.com/convert-array-to-list-and-list-to-array)
