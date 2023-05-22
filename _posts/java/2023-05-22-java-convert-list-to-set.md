---
title: Java List에서 Set 변환
author: dejavuhyo
date: 2023-05-22 09:20:00 +0900
categories: [Language, Java]
tags: [java-convert, list-set, 리스트-셋, 자바-변환]
---

## 1. List를 Set으로 변환

### 1) 일반 자바 사용
일반 Java를 사용하여 List를 Set으로 변환하는 방법이다.

```java
public void givenUsingCoreJava_whenListConvertedToSet_thenCorrect() {
    List<Integer> sourceList = Arrays.asList(0, 1, 2, 3, 4, 5);
    Set<Integer> targetSet = new HashSet<>(sourceList);
}
```

보시다시피 변환 프로세스는 각 컬렉션의 생성자가 다른 컬렉션을 소스로 받아들이기 때문에 형식이 안전하고 간단하다.

### 2) 구아바 사용
Guava를 사용하여 동일한 변환을 수행한다.

```java
public void givenUsingGuava_whenListConvertedToSet_thenCorrect() {
    List<Integer> sourceList = Lists.newArrayList(0, 1, 2, 3, 4, 5);
    Set<Integer> targetSet = Sets.newHashSet(sourceList);
}
```

### 3) Apache Commons Collections 사용
Apache의 Commons Collection API를 사용하여 List에서 Set으로 변환한다.

```java
public void givenUsingCommonsCollections_whenListConvertedToSet_thenCorrect() {
    List<Integer> sourceList = Lists.newArrayList(0, 1, 2, 3, 4, 5);
    Set<Integer> targetSet = new HashSet<>(6);
    CollectionUtils.addAll(targetSet, sourceList);
}
```

### 4) 자바 10
한 가지 추가 옵션은 Java 10에 도입된 `Set.copyOf` 정적 팩터리 메서드를 사용하는 것이다.

```java
public void givenUsingJava10_whenListConvertedToSet_thenCorrect() {
    List sourceList = Lists.newArrayList(0, 1, 2, 3, 4, 5);
    Set targetSet = Set.copyOf(sourceList);
}
```

이 방법으로 생성된 Set은 수정할 수 없다.

## 2. Set을 List로 변환

### 1) 일반 자바 사용
Java를 사용하여 Set에서 List로 역변환을 수행한다.

```java
public void givenUsingCoreJava_whenSetConvertedToList_thenCorrect() {
   Set<Integer> sourceSet = Sets.newHashSet(0, 1, 2, 3, 4, 5);
   List<Integer> targetList = new ArrayList<>(sourceSet);
}
```

### 2) 구아바 사용
Guava 솔루션을 사용하여 동일한 작업을 수행할 수 있다.

```java
public void givenUsingGuava_whenSetConvertedToList_thenCorrect() {
    Set<Integer> sourceSet = Sets.newHashSet(0, 1, 2, 3, 4, 5);
    List<Integer> targetList = Lists.newArrayList(sourceSet);
}
```

이것은 Java 접근 방식과 매우 유사하지만 중복 코드가 약간 적다.

### 3) Apache Commons Collections 사용
Set과 List 사이를 변환하는 Commons Collections 솔루션이다.

```java
public void givenUsingCommonsCollections_whenSetConvertedToList_thenCorrect() {
    Set<Integer> sourceSet = Sets.newHashSet(0, 1, 2, 3, 4, 5);
    List<Integer> targetList = new ArrayList<>(6);
    CollectionUtils.addAll(targetList, sourceSet);
}
```

### 4) 자바 10
Java 10에 도입된 `List.copyOf`를 사용할 수 있다.

```java
public void givenUsingJava10_whenSetConvertedToList_thenCorrect() {
    Set<Integer> sourceSet = Sets.newHashSet(0, 1, 2, 3, 4, 5);
    List<Integer> targetList = List.copyOf(sourceSet);
}
```

결과 List는 수정할 수 없다.

## [출처 및 참고]
* [https://www.baeldung.com/convert-list-to-set-and-set-to-list](https://www.baeldung.com/convert-list-to-set-and-set-to-list)
