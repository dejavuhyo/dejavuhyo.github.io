---
title: Java List 중복 제거
author: dejavuhyo
date: 2023-03-08 09:45:00 +0900
categories: [Language, Java]
tags: [java-list, list-duplicates, list-duplicates-removing, 자바-리스트, 리스트-중복-제거, 중복-제거, 리스트-중복]
---

## 1. 일반 Java를 사용하여 List 중복 제거
`Set`을 통해 표준 Java Collections Framework를 사용하여 List에서 중복 요소를 쉽게 제거할 수 있다.

```java
public void givenListContainsDuplicates_whenRemovingDuplicatesWithPlainJava_thenCorrect() {
    List<Integer> listWithDuplicates = Lists.newArrayList(5, 0, 3, 1, 2, 3, 0, 0);
    List<Integer> listWithoutDuplicates = new ArrayList<>(
      new HashSet<>(listWithDuplicates));

    assertThat(listWithoutDuplicates, hasSize(5));
    assertThat(listWithoutDuplicates, containsInAnyOrder(5, 0, 3, 1, 2));
}
```

원래 목록은 변경되지 않은 상태로 유지된다.

위의 예에서는 정렬되지 않은 컬렉션인 `HashSet` 구현을 사용했다. 결과적으로 *정리된 `listWithoutDuplicates`의 순서는 원래 `listWithDuplicates`의 순서와 다를 수 있다.*

순서를 유지해야 하는 경우 `LinkedHashSet`을 대신 사용할 수 있다.

```java
public void givenListContainsDuplicates_whenRemovingDuplicatesPreservingOrderWithPlainJava_thenCorrect() {
    List<Integer> listWithDuplicates = Lists.newArrayList(5, 0, 3, 1, 2, 3, 0, 0);
    List<Integer> listWithoutDuplicates = new ArrayList<>(
      new LinkedHashSet<>(listWithDuplicates));

    assertThat(listWithoutDuplicates, hasSize(5));
    assertThat(listWithoutDuplicates, containsInRelativeOrder(5, 0, 3, 1, 2));
}
```

## 2. Guava를 사용하여 List 중복 제거
Guava를 사용하여 동일한 작업을 수행할 수 있다.

```java
public void givenListContainsDuplicates_whenRemovingDuplicatesWithGuava_thenCorrect() {
    List<Integer> listWithDuplicates = Lists.newArrayList(5, 0, 3, 1, 2, 3, 0, 0);
    List<Integer> listWithoutDuplicates 
      = Lists.newArrayList(Sets.newHashSet(listWithDuplicates));

    assertThat(listWithoutDuplicates, hasSize(5));
    assertThat(listWithoutDuplicates, containsInAnyOrder(5, 0, 3, 1, 2));
}
```

원래 목록은 변경되지 않는다.

다시 정리된 목록의 요소 순서는 임의적일 수 있다.

`LinkedHashSet` 구현을 사용하는 경우 초기 순서를 유지한다.

```java
public void givenListContainsDuplicates_whenRemovingDuplicatesPreservingOrderWithGuava_thenCorrect() {
    List<Integer> listWithDuplicates = Lists.newArrayList(5, 0, 3, 1, 2, 3, 0, 0);
    List<Integer> listWithoutDuplicates 
      = Lists.newArrayList(Sets.newLinkedHashSet(listWithDuplicates));

    assertThat(listWithoutDuplicates, hasSize(5));
    assertThat(listWithoutDuplicates, containsInRelativeOrder(5, 0, 3, 1, 2));
}
```

## 3. Java 8 Lambdas를 사용하여 List 중복 제거 
Java 8에서 Lambda를 사용하는 방법이다. `equals()` 메서드에서 반환된 결과를 기반으로 고유한 요소로 구성된 스트림을 반환하는 Stream API의 `distinct()` 메서드를 사용한다.

또한 순서가 지정된 스트림의 경우 개별 요소의 선택이 안정적이다. 이는 중복된 요소의 경우 발생 순서에서 처음 나타나는 요소가 보존됨을 의미한다.

```java
public void givenListContainsDuplicates_whenRemovingDuplicatesWithJava8_thenCorrect() {
    List<Integer> listWithDuplicates = Lists.newArrayList(5, 0, 3, 1, 2, 3, 0, 0);
    List<Integer> listWithoutDuplicates = listWithDuplicates.stream()
     .distinct()
     .collect(Collectors.toList());

    assertThat(listWithoutDuplicates, hasSize(5));
    assertThat(listWithoutDuplicates, containsInAnyOrder(5, 0, 3, 1, 2));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-remove-duplicates-from-list](https://www.baeldung.com/java-remove-duplicates-from-list)
