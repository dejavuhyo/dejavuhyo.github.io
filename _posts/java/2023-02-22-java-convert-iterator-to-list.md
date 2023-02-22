---
title: Java Iterator를 List로 변환
author: dejavuhyo
date: 2023-02-22 21:45:00 +0900
categories: [Language, Java]
tags: [java-convert-iterator-list, java-convert, iterator-list, java-iterator, java-list, 반복자-목록]
---

## 1. Iterator를 List로 변환
Iterator를 List로 변환하는 방법이다. while 루프, Java 8 및 몇 가지 공통 라이브러리를 사용한다.

Integers와 함께 Iterator를 사용한다.

```java
Iterator<Integer> iterator = Arrays.asList(1, 2, 3).iterator();
```

## 2. While 루프 사용
Java 8 이전에 전통적으로 사용된 접근 방식이다. while 루프를 사용하여 Iterator를 List로 변환한다.

```java
List<Integer> actualList = new ArrayList<Integer>();
while (iterator.hasNext()) {
    actualList.add(iterator.next());
}

assertThat(actualList, containsInAnyOrder(1, 2, 3));
```

## 3. Iterator.forEachRemaining 사용
Java 8 이상에서는 Iterator의 `forEachRemaining()` 메서드를 사용하여 List를 작성할 수 있다. List 인터페이스의 `add()` 메서드를 메서드 참조로 전달한다.

```java
List<Integer> actualList = new ArrayList<Integer>();
iterator.forEachRemaining(actualList::add);

assertThat(actualList, containsInAnyOrder(1, 2, 3));
```

## 4. 스트림 API 사용
Java 8 Streams API를 사용하여 Iterator를 List로 변환한다. Stream API를 사용하려면 먼저 Iterator를 Iterable로 변환 해야한다. Java 8 Lambda 표현식을 사용하여 이를 수행할 수 있다.

```java
Iterable<Integer> iterable = () -> iterator;
```

StreamSupport 클래스의 `stream()` 및 `collect()` 메서드를 사용하여 List를 빌드 할 수 있다.

```java
List<Integer> actualList = StreamSupport
  .stream(iterable.spliterator(), false)
  .collect(Collectors.toList());

assertThat(actualList, containsInAnyOrder(1, 2, 3));
```

## 5. 구아바 사용
Google의 Guava 라이브러리는 변경 가능한 목록과 변경 불가능한 목록을 모두 생성하는 옵션을 제공하므로 두 가지 접근 방식이 있다.

먼저 `ImmutableList.copyOf()` 메서드를 사용하여 불변 목록을 만든다.

```java
List<Integer> actualList = ImmutableList.copyOf(iterator);

assertThat(actualList, containsInAnyOrder(1, 2, 3));
```

이제 `Lists.newArrayList()` 메서드를 사용하여 변경 가능한 목록을 만든다.

```java
List<Integer> actualList = Lists.newArrayList(iterator);

assertThat(actualList, containsInAnyOrder(1, 2, 3));
```

## 6. Apache Commons 사용
Apache Commons Collections 라이브러리는 목록에서 작업할 수 있는 옵션을 제공한다. `IteratorUtils`를 사용하여 변환을 수행한다.

```java
List<Integer> actualList = IteratorUtils.toList(iterator);

assertThat(actualList, containsInAnyOrder(1, 2, 3));
```

## [출처 및 참고]
* [https://www.baeldung.com/java-convert-iterator-to-list](https://www.baeldung.com/java-convert-iterator-to-list)
