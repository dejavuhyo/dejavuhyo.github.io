---
title: Apache Commons Collections Bag
author: dejavuhyo
date: 2023-06-27 08:50:00 +0900
categories: [Language, Java]
tags: [apache-commons, apache-commons-bag, bag, 아파치-커먼즈]
---

## 1. 메이븐 종속성
[Maven Central](https://central.sonatype.com/artifact/org.apache.commons/commons-collections4)에서 최신 종속 항목을 가져온다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.4</version>
</dependency>
```

## 2. Bags vs Collections
Bag은 반복 횟수와 함께 여러 항목을 저장할 수 있는 모음이다.

```java
public void whenAdded_thenCountIsKept() {
    Bag<Integer> bag = new HashBag<>(Arrays.asList(1, 2, 3, 3, 3, 1, 4));
    assertThat(2, equalTo(bag.getCount(1)));
}
```

## 3. 징수 계약 위반
Bag의 API 문서를 읽는 동안 일부 메서드가 표준 Java의 Collection 계약을 위반하는 것으로 표시되어 있음을 알 수 있다.

예를 들어 Java 컬렉션에서 `add()` API를 사용할 때 항목이 이미 컬렉션에 있더라도 true를 받는다.

```java
Collection<Integer> collection = new ArrayList<>();
collection.add(1);
assertThat(collection.add(1), is(true));
```

Bag 구현의 동일한 API는 컬렉션에서 이미 사용 가능한 요소를 추가할 때 false를 반환한다.

```java
Bag<Integer> bag = new HashBag<>();
bag.add(1);

assertThat(bag.add(1), is(not(true)));
```

이러한 문제를 해결하기 위해 Apache Collections의 라이브러리는 CollectionBag라는 데코레이터를 제공한다. 이를 사용하여 Bag 컬렉션이 Java 컬렉션 계약을 준수하도록 만들 수 있다.

```java
public void whenBagAddAPILikeCollectionAPI_thenTrue() {
    Bag<Integer> bag = CollectionBag.collectionBag(new HashBag<>());
    bag.add(1);

    assertThat(bag.add(1), is((true)));
}
```

## 4. Bag 구현
Apache의 컬렉션 라이브러리 내에서 Bag 인터페이스의 다양한 구현이다.

### 1) HashBag
요소를 추가하고 이 요소가 Bag 컬렉션에 있어야 하는 사본 수에 대해 API에 지시할 수 있다.

```java
public void givenAdd_whenCountOfElementsDefined_thenCountAreAdded() {
    Bag<Integer> bag = new HashBag<>();
    bag.add(1, 5); // adding 1 five times
    assertThat(5, equalTo(bag.getCount(1)));
}
```

또한 Bag에서 특정 수의 사본 또는 요소의 모든 인스턴스를 삭제할 수 있다.

```java
public void givenMultipleCopies_whenRemove_allAreRemoved() {
    Bag<Integer> bag = new HashBag<>(Arrays.asList(1, 2, 3, 3, 3, 1, 4));

    bag.remove(3, 1); // remove one element, two still remain
    assertThat(2, equalTo(bag.getCount(3)));

    bag.remove(1); // remove all
    assertThat(0, equalTo(bag.getCount(1)));
}
```

### 2) TreeBag
TreeBag 구현은 다른 트리처럼 작동하며 Bag 의미 체계를 추가로 유지한다.

자연스럽게 정수 배열을 TreeBag로 정렬한 다음 각 개별 요소가 컬렉션 내에 있는 인스턴스 수를 쿼리할 수 있다.

```java
public void givenTree_whenDuplicateElementsAdded_thenSort() {
    TreeBag<Integer> bag = new TreeBag<>(Arrays.asList(7, 5, 1, 7, 2, 3, 3, 3, 1, 4, 7));
    assertThat(bag.first(), equalTo(1));
    assertThat(bag.getCount(bag.first()), equalTo(2));
    assertThat(bag.last(), equalTo(7));
    assertThat(bag.getCount(bag.last()), equalTo(3));
}
```

TreeBag는 SortedBag 인터페이스를 구현하며, 이 인터페이스의 모든 구현은 데코레이터 CollectionSortedBag를 사용하여 Java Collections 계약을 준수할 수 있다.

```java
public void whenTreeAddAPILikeCollectionAPI_thenTrue() {
    SortedBag<Integer> bag = CollectionSortedBag.collectionSortedBag(new TreeBag<>());
    bag.add(1);
    assertThat(bag.add(1), is((true)));
}
```

### 3) SynchronizedSortedBag
널리 사용되는 또 다른 Bag 구현은 SynchronizedSortedBag이다. 정확하게 이것은 SortedBag 구현의 동기화된 데코레이터이다.

이 데코레이터를 TreeBag(SortedBag 구현)와 함께 사용하여 백에 대한 액세스를 동기화할 수 있다.

```java
public void givenSortedBag_whenDuplicateElementsAdded_thenSort() {
    SynchronizedSortedBag<Integer> bag = SynchronizedSortedBag.synchronizedSortedBag(new TreeBag<>(Arrays.asList(7, 5, 1, 7, 2, 3, 3, 3, 1, 4, 7)));
    assertThat(bag.first(), equalTo(1));
    assertThat(bag.getCount(bag.first()), equalTo(2));
    assertThat(bag.last(), equalTo(7));
    assertThat(bag.getCount(bag.last()), equalTo(3));
}
```

API(`Collections.synchronizedSortedMap()` 및 TreeMap)의 조합을 사용하여 SynchronizedSortedBag로 수행한 작업을 시뮬레이션할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/apache-commons-bag](https://www.baeldung.com/apache-commons-bag)
