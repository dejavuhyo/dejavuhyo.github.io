---
title: Java ArrayList에 여러 항목 추가
author: dejavuhyo
date: 2023-02-07 21:35:00 +0900
categories: [Language, Java]
tags: [java-copy-list, copy-list, java-list, 자바-리스트-복사, 리스트-복사, 자바-리스트]
---

## 1. AddAll
먼저 ArrayList에 여러 항목을 추가하는 간단한 방법이다.

먼저 컬렉션을 인수로 사용하는 `addAll()`을 사용한다.

```java
List<Integer> anotherList = Arrays.asList(5, 12, 9, 3, 15, 88);
list.addAll(anotherList);
```

**첫 번째 목록에 추가된 요소는 anotherList의 요소와 동일한 개체를 참조한다.**

이러한 이유로 이러한 요소 중 하나에 대한 모든 수정 사항은 두 목록 모두에 영향을 미친다.

## 2. Collections.addAll
Collections 클래스는 컬렉션에서 작동하거나 컬렉션을 반환하는 정적 메서드로만 구성된다.

그중 하나는 대상 목록이 필요한 `addAll`이며 추가할 항목은 개별적으로 또는 배열로 지정할 수 있다.

다음은 개별 요소와 함께 사용하는 방법의 예이다.

```java
List<Integer> list = new ArrayList<>();
Collections.addAll(list, 1, 2, 3, 4, 5);
```

두 개의 어레이로 작업을 예시하는 또 다른 하나는 다음과 같다.

```java
List<Integer> list = new ArrayList<>();
Integer[] otherList = new Integer[] {1, 2, 3, 4, 5};
Collections.addAll(list, otherList);
```

위 섹션에서 설명한 방법과 유사하게 여기서 **두 목록의 내용은 동일한 객체를 참조한다.**

## 3. Java 8 사용
다음 예제는 Stream이다.

```java
List<Integer> source = ...;
List<Integer> target = ...;

source.stream()
  .forEachOrdered(target::add);
```

이 방법의 주요 장점은 건너뛰기와 필터를 사용할 수 있다는 것이다. 다음 예에서는 첫 번째 요소를 건너뛴다.

```java
source.stream()
  .skip(1)
  .forEachOrdered(target::add);
```

필요에 따라 요소를 필터링하는 것이 가능하다. 예를 들어 정수 값은 다음과 같다.

```java
source.stream()
  .filter(i -> i > 10)
  .forEachOrdered(target::add);
```

마지막으로 `null-safe` 방식으로 작업하려는 시나리오가 있다. 그런 경우에는 Optional을 사용할 수 있다.

```java
Optional.ofNullable(source).ifPresent(target::addAll)
```

위의 예는 `addAll` 메서드를 사용하여 source에서 target으로 요소를 추가하고 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-add-items-array-list](https://www.baeldung.com/java-add-items-array-list)
