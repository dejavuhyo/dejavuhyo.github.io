---
title: Java Array에서 Set 변환
author: dejavuhyo
date: 2023-05-18 09:30:00 +0900
categories: [Language, Java]
tags: [java-convert, array-set, 어레이-셋, 자바-변환]
---

## 1. List를 Set으로 변환

### 1) 일반 자바 사용
일반 Java를 사용하여 Array를 Set으로 바꾸는 방법이다.

```java
@Test
public void givenUsingCoreJavaV1_whenArrayConvertedToSet_thenCorrect() {
    Integer[] sourceArray = { 0, 1, 2, 3, 4, 5 };
    Set<Integer> targetSet = new HashSet<Integer>(Arrays.asList(sourceArray));
}
```

또는 Set을 먼저 만든 다음 Array 요소로 채울 수 있다.

```java
@Test
public void givenUsingCoreJavaV2_whenArrayConvertedToSet_thenCorrect() {
    Integer[] sourceArray = { 0, 1, 2, 3, 4, 5 };
    Set<Integer> targetSet = new HashSet<Integer>();
    Collections.addAll(targetSet, sourceArray);
}
```

### 2) 구아바 사용
구아바를 사용하여 Array에서 Set으로 변환한다.

```java
@Test
public void givenUsingGuava_whenArrayConvertedToSet_thenCorrect() {
    Integer[] sourceArray = { 0, 1, 2, 3, 4, 5 };
    Set<Integer> targetSet = Sets.newHashSet(sourceArray);
}
```

### 3) Apache Commons Collections 사용
Apache의 Commons Collection 라이브러리를 사용하여 변환한다.

```java
@Test
public void givenUsingCommonsCollections_whenArrayConvertedToSet_thenCorrect() {
    Integer[] sourceArray = { 0, 1, 2, 3, 4, 5 };
    Set<Integer> targetSet = new HashSet<>(6);
    CollectionUtils.addAll(targetSet, sourceArray);
}
```

## 2. Set을 List로 변환

### 1) 일반 자바 사용
반대로 기존 Set을 Array로 변환하는 방법이다.

```java
@Test
public void givenUsingCoreJava_whenSetConvertedToArray_thenCorrect() {
    Set<Integer> sourceSet = Sets.newHashSet(0, 1, 2, 3, 4, 5);
    Integer[] targetArray = sourceSet.toArray(new Integer[0]);
}
```

`toArray (new T[0])`는 `toArray(new T[size])` 보다 메서드를 사용하는 기본 방법이다. `Aleksey Shipilëv`가 자신의 [블로그 게시물](https://shipilev.net/blog/2016/arrays-wisdom-ancients/#_conclusion)에서 증명했듯이 더 빠르고 안전하며 깨끗하다.

### 2) 구아바 사용
구아바 솔루션을 사용한다.

```java
@Test
public void givenUsingGuava_whenSetConvertedToArray_thenCorrect() {
    Set<Integer> sourceSet = Sets.newHashSet(0, 1, 2, 3, 4, 5);
    int[] targetArray = Ints.toArray(sourceSet);
}
```

Guava의 Ints API를 사용하고 있으므로 이 솔루션은 작업 중인 데이터 유형에 따라 다르다.

## [출처 및 참고]
* [https://www.baeldung.com/convert-array-to-set-and-set-to-array](https://www.baeldung.com/convert-array-to-set-and-set-to-array)
