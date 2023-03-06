---
title: Java List 분할
author: dejavuhyo
date: 2023-03-06 14:55:00 +0900
categories: [Language, Java]
tags: [java-partition-list, partition-list, list-split, java-list, 자바-목록-분할, 목록-분할, 리스트-분할, 자바-리스트]
---

## 1. Partition a List
List를 주어진 크기의 여러 하위 목록으로 분할하는 방법이다.

상대적으로 간단한 작업의 경우 표준 Java 컬렉션 API에서 지원하지 않는다. 운 좋게도 Guava와 Apache Commons Collections 모두 유사한 방식으로 작업을 구현했다.

## 2. Guava를 사용하여 목록 분할
Guava는 `Lists.partition` 작업을 통해 List를 지정된 크기의 하위 목록으로 분할하는 것을 용이하게 한다.

```java
@Test
public void givenList_whenParitioningIntoNSublists_thenCorrect() {
    List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);
    List<List<Integer>> subSets = Lists.partition(intList, 3);

    List<Integer> lastPartition = subSets.get(2);
    List<Integer> expectedLastPartition = Lists.<Integer> newArrayList(7, 8);
    assertThat(subSets.size(), equalTo(3));
    assertThat(lastPartition, equalTo(expectedLastPartition));
}
```

## 3. Guava를 사용하여 컬렉션 분할
Guava를 사용하여 컬렉션을 분할 할 수도 있다.

```java
@Test
public void givenCollection_whenParitioningIntoNSublists_thenCorrect() {
    Collection<Integer> intCollection = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);

    Iterable<List<Integer>> subSets = Iterables.partition(intCollection, 3);

    List<Integer> firstPartition = subSets.iterator().next();
    List<Integer> expectedLastPartition = Lists.<Integer> newArrayList(1, 2, 3);
    assertThat(firstPartition, equalTo(expectedLastPartition));
}
```

파티션은 원본 컬렉션의 하위 목록 보기이므로 원본 컬렉션의 변경 사항이 파티션에 반영된다.

```java
@Test
public void givenListPartitioned_whenOriginalListIsModified_thenPartitionsChangeAsWell() {
    // Given
    List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);
    List<List<Integer>> subSets = Lists.partition(intList, 3);

    // When
    intList.add(9);

    // Then
    List<Integer> lastPartition = subSets.get(2);
    List<Integer> expectedLastPartition = Lists.<Integer> newArrayList(7, 8, 9);
    assertThat(lastPartition, equalTo(expectedLastPartition));
}
```

## 4. Apache Commons Collections를 사용하여 목록 분할
Apache Commons Collections의 최신 릴리스에는 최근 목록 분할에 대한 지원이 추가되었다.

```java
@Test
public void givenList_whenParitioningIntoNSublists_thenCorrect() {
    List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);
    List<List<Integer>> subSets = ListUtils.partition(intList, 3);

    List<Integer> lastPartition = subSets.get(2);
    List<Integer> expectedLastPartition = Lists.<Integer> newArrayList(7, 8);
    assertThat(subSets.size(), equalTo(3));
    assertThat(lastPartition, equalTo(expectedLastPartition));
}
```

Commons Collections에는 Guava `Iterables.partition`과 유사한 원시 컬렉션을 분할하는 해당 옵션이 없다.

마지막으로 동일한 경고가 여기에도 적용된다. 결과 파티션은 원래 목록의 뷰이다.

## 5. Java8을 사용하여 목록 분할
Java8을 사용하여 목록을 분할하는 방법이다.

### 1) Collectors partitioningBy
`Collectors.partitioningBy()`를 사용하여 목록을 2개의 하위 목록으로 나눌 수 있다.

```java
@Test
public void givenList_whenParitioningIntoSublistsUsingPartitionBy_thenCorrect() {
    List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);

    Map<Boolean, List<Integer>> groups = 
      intList.stream().collect(Collectors.partitioningBy(s -> s > 6));
    List<List<Integer>> subSets = new ArrayList<List<Integer>>(groups.values());

    List<Integer> lastPartition = subSets.get(1);
    List<Integer> expectedLastPartition = Lists.<Integer> newArrayList(7, 8);
    assertThat(subSets.size(), equalTo(2));
    assertThat(lastPartition, equalTo(expectedLastPartition));
}
```

> 참고: 결과 파티션은 기본 목록 보기가 아니므로 기본 목록에 발생하는 모든 변경 사항은 파티션에 영향을 주지 않는다.

### 2) Collectors groupingBy
`Collectors.groupingBy()`를 사용하여 목록을 여러 파티션으로 나눌 수 있다.

```java
@Test
public final void givenList_whenParitioningIntoNSublistsUsingGroupingBy_thenCorrect() {
    List<Integer> intList = Lists.newArrayList(1, 2, 3, 4, 5, 6, 7, 8);

    Map<Integer, List<Integer>> groups = 
      intList.stream().collect(Collectors.groupingBy(s -> (s - 1) / 3));
    List<List<Integer>> subSets = new ArrayList<List<Integer>>(groups.values());

    List<Integer> lastPartition = subSets.get(2);
    List<Integer> expectedLastPartition = Lists.<Integer> newArrayList(7, 8);
    assertThat(subSets.size(), equalTo(3));
    assertThat(lastPartition, equalTo(expectedLastPartition));
}
```

> 참고: `Collectors.partitioningBy()`와 마찬가지로 결과 파티션은 기본 목록의 변경 사항에 영향을 받지 않는다.

### 3) 구분 기호로 목록 분할
Java8을 사용하여 목록을 구분 기호로 나눌 수도 있다.

```java
@Test
public void givenList_whenSplittingBySeparator_thenCorrect() {
    List<Integer> intList = Lists.newArrayList(1, 2, 3, 0, 4, 5, 6, 0, 7, 8);

    int[] indexes = 
      Stream.of(IntStream.of(-1), IntStream.range(0, intList.size())
      .filter(i -> intList.get(i) == 0), IntStream.of(intList.size()))
      .flatMapToInt(s -> s).toArray();
    List<List<Integer>> subSets = 
      IntStream.range(0, indexes.length - 1)
               .mapToObj(i -> intList.subList(indexes[i] + 1, indexes[i + 1]))
               .collect(Collectors.toList());

    List<Integer> lastPartition = subSets.get(2);
    List<Integer> expectedLastPartition = Lists.<Integer> newArrayList(7, 8);
    assertThat(subSets.size(), equalTo(3));
    assertThat(lastPartition, equalTo(expectedLastPartition));
}
```

> 참고: "0"을 구분 기호로 사용했다. 먼저 List의 모든 "0" 요소 인덱스를 얻은 다음 이 인덱스에서 List를 분할한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-list-split](https://www.baeldung.com/java-list-split)
