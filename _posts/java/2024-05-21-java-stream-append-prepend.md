---
title: Java 스트림 단일 요소 추가
author: dejavuhyo
date: 2024-05-21 12:31:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-append, stream-prepend, 자바-스트림, 스트림-요소-추가]
---

## 1. 앞에 추가
정적 `Stream.concat()`을 호출하면 주어진 요소를 Stream 앞에 쉽게 추가할 수 있다.

```java
@Test
public void givenStream_whenPrependingObject_thenPrepended() {
    Stream<Integer> anStream = Stream.of(1, 2, 3, 4, 5);
    Stream<Integer> newStream = Stream.concat(Stream.of(99), anStream);
    assertEquals(newStream.findFirst().get(), (Integer) 99);
}
```

## 2. 추가
마찬가지로 Stream의 끝에 요소를 추가하려면 인수를 반전시키기만 하면 된다.

Streams는 무한 시퀀스를 나타낼 수 있으므로 새 요소에 도달하지 못할 수도 있는 시나리오가 있다.

```java
@Test
public void givenStream_whenAppendingObject_thenAppended() {
    Stream<String> anStream = Stream.of("a", "b", "c", "d", "e");
    Stream<String> newStream = Stream.concat(anStream, Stream.of("A"));
    List<String> resultList = newStream.collect(Collectors.toList());
    assertEquals(resultList.get(resultList.size() - 1), "A");
}
```

## 3. 특정 지수
Java의 스트림(`java.util.stream`)은 순차 및 병렬 집계 작업을 지원하는 일련의 요소이다. 특정 인덱스에 값을 추가하는 기능은 해당 기능을 위해 설계되지 않았기 때문에 없다. 그러나 이를 달성하는 몇 가지 방법이 있다.

한 가지 접근 방식은 터미널 작업을 사용하여 스트림 값을 ArrayList로 수집한 다음 단순히 add(int index, E element) 메서드를 사용하는 것이다. 이렇게 하면 원하는 결과를 얻을 수 있지만 새 요소를 삽입하기 전에 Stream을 사용해야 하기 때문에 Stream의 laziness도 잃게 된다.

대안은 Spliterator를 사용하고 요소를 대상 인덱스까지 순차적으로 반복하는 것이다(Spliterator 클래스 반복기 사용). 개념은 스트림을 두 개의 스트림(A와 B)으로 나누는 것이다. 스트림 A는 인덱스 0부터 대상 인덱스까지 시작하고 스트림 B는 나머지 요소이다. 그런 다음 스트림 A에 요소를 추가하고 스트림 A와 B를 연결한다.

이 경우 스트림은 탐욕스럽게 소비되지 않으므로 완전히 게으르다. 일정한 시간에 특정 인덱스에 요소를 삽입할 수 있다.

```java
private static  Stream insertInStream(Stream stream, T elem, int index) {
    Spliterator spliterator = stream.spliterator();
    Iterator iterator = Spliterators.iterator(spliterator);

    return Stream.concat(Stream.concat(Stream.generate(iterator::next)
      .limit(index), Stream.of(elem)), StreamSupport.stream(spliterator, false));
}
```

코드를 테스트하여 모든 것이 예상대로 작동하는지 확인한다.

```java
@Test
public void givenStream_whenInsertingObject_thenInserted() {
    Stream<Double> anStream = Stream.of(1.1, 2.2, 3.3);
    Stream<Double> newStream = insertInStream(anStream, 9.9, 3);

    List<Double> resultList = newStream.collect(Collectors.toList());
 
    assertEquals(resultList.get(3), (Double) 9.9);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-append-prepend](https://www.baeldung.com/java-stream-append-prepend)
