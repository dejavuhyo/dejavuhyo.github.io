---
title: Java 스트림 병합
author: dejavuhyo
date: 2023-11-22 22:10:00 +0900
categories: [Language, Java]
tags: [java-stream, merge-streams, 자바-스트림, 스트림-병합]
---

## 1. 일반 자바 사용
JDK 8 Stream 클래스에는 몇 가지 유용한 정적 유틸리티 메서드가 있다. `concat()` 메서드를 살펴본다.

### 1) 두 스트림 병합
Stream 2개를 결합하는 가장 간단한 방법은 정적 `Stream.concat()` 메서드를 사용하는 것이다.

```java
@Test
public void whenMergingStreams_thenResultStreamContainsElementsFromBoth() {
    Stream<Integer> stream1 = Stream.of(1, 3, 5);
    Stream<Integer> stream2 = Stream.of(2, 4, 6);

    Stream<Integer> resultingStream = Stream.concat(stream1, stream2);

    assertEquals(
      Arrays.asList(1, 3, 5, 2, 4, 6),
      resultingStream.collect(Collectors.toList()));
}
```

### 2) 여러 스트림 병합
2개 이상의 스트림을 병합해야 하면 상황이 좀 더 복잡해진다. 한 가지 가능성은 처음 두 스트림을 연결한 후 그 결과를 다음 스트림과 연결하는 것이다.

```java
@Test
public void given3Streams_whenMerged_thenResultStreamContainsAllElements() {
    Stream<Integer> stream1 = Stream.of(1, 3, 5);
    Stream<Integer> stream2 = Stream.of(2, 4, 6);
    Stream<Integer> stream3 = Stream.of(18, 15, 36);

    Stream<Integer> resultingStream = Stream.concat(
      Stream.concat(stream1, stream2), stream3);

    assertEquals(
      Arrays.asList(1, 3, 5, 2, 4, 6, 18, 15, 36),
      resultingStream.collect(Collectors.toList()));
}
```

보시다시피, 이 접근 방식은 더 많은 스트림에서 실행 불가능해진다. 물론 중간 변수나 도우미 메서드를 만들어 더 읽기 쉽게 만들 수 있지만 더 나은 옵션은 다음과 같다.

```java
@Test
public void given4Streams_whenMerged_thenResultStreamContainsAllElements() {
    Stream<Integer> stream1 = Stream.of(1, 3, 5);
    Stream<Integer> stream2 = Stream.of(2, 4, 6);
    Stream<Integer> stream3 = Stream.of(18, 15, 36);
    Stream<Integer> stream4 = Stream.of(99);

    Stream<Integer> resultingStream = Stream.of(
      stream1, stream2, stream3, stream4)
      .flatMap(i -> i);

    assertEquals(
      Arrays.asList(1, 3, 5, 2, 4, 6, 18, 15, 36, 99),
      resultingStream.collect(Collectors.toList()));
}
```

여기서 일어나는 일은 다음과 같다.

* 먼저 4개의 스트림을 포함하는 새 스트림을 생성한다. 그 결과 `Stream<Stream<Integer>>`가 생성된다.

* 그런 다음 ID 함수를 사용하여 이를 `Stream<Integer>`로 `flatMap()` 한다.

## 2. StreamEx 사용하기
[StreamEx](https://github.com/amaembo/streamex)는 Java 8 Streams의 가능성을 확장하는 오픈 소스 Java 라이브러리이다. JDK의 Stream 인터페이스에 대한 향상된 기능으로 StreamEx 클래스를 사용한다.

### 1) 스트림 병합
StreamEx 라이브러리를 사용하면 `Append()` 인스턴스 메서드를 사용하여 스트림을 병합할 수 있다.

```java
@Test
public void given4Streams_whenMerged_thenResultStreamContainsAllElements() {
    Stream<Integer> stream1 = Stream.of(1, 3, 5);
    Stream<Integer> stream2 = Stream.of(2, 4, 6);
    Stream<Integer> stream3 = Stream.of(18, 15, 36);
    Stream<Integer> stream4 = Stream.of(99);

    Stream<Integer> resultingStream = StreamEx.of(stream1)
      .append(stream2)
      .append(stream3)
      .append(stream4);

    assertEquals(
      Arrays.asList(1, 3, 5, 2, 4, 6, 18, 15, 36, 99),
      resultingStream.collect(Collectors.toList()));
}
```

인스턴스 메서드이므로 쉽게 연결하고 여러 스트림을 추가할 수 있다.

StreamEx 유형에 resultStream 변수를 입력하는 경우 `toList()`를 사용하여 스트림에서 목록을 생성할 수도 있다.

### 2) prepend()를 사용하여 스트림 병합
StreamEx에는 요소를 서로 앞에 추가하는 `prepend()`라는 메서드도 포함되어 있다.

```java
@Test
public void given3Streams_whenPrepended_thenResultStreamContainsAllElements() {
    Stream<String> stream1 = Stream.of("foo", "bar");
    Stream<String> openingBracketStream = Stream.of("[");
    Stream<String> closingBracketStream = Stream.of("]");

    Stream<String> resultingStream = StreamEx.of(stream1)
      .append(closingBracketStream)
      .prepend(openingBracketStream);

    assertEquals(
      Arrays.asList("[", "foo", "bar", "]"),
      resultingStream.collect(Collectors.toList()));
}
```

## 3. Jooλ 사용하기
[jOOL](https://github.com/jOOQ/jOOL)람은 JDK에 유용한 확장을 제공하는 JDK 8 호환 라이브러리이다. 여기서 가장 중요한 스트림 추상화는 Seq 이다. 이는 순차적이고 순서가 지정된 스트림이므로 `Parallel()`을 호출해도 아무런 효과가 없다.

### 1) 스트림 병합
StreamEx 라이브러리와 마찬가지로 jOOLA에도 `Append()` 메소드가 있다.

```java
@Test
public void given2Streams_whenMerged_thenResultStreamContainsAllElements() {
    Stream<Integer> seq1 = Stream.of(1, 3, 5);
    Stream<Integer> seq2 = Stream.of(2, 4, 6);

    Stream<Integer> resultingSeq = Seq.ofType(seq1, Integer.class)
      .append(seq2);

    assertEquals(
      Arrays.asList(1, 3, 5, 2, 4, 6),
      resultingSeq.collect(Collectors.toList()));
}
```

또한 jOOLA Seq 유형에 resultSeq 변수를 입력하면 편리한 `toList()` 메소드가 있다.

### 2) prepend()를 사용하여 스트림 병합
`append()` 메소드가 존재하므로 jOOLA에도 `prepend()` 메소드가 있다.

```java
@Test
public void given3Streams_whenPrepending_thenResultStreamContainsAllElements() {
    Stream<String> seq = Stream.of("foo", "bar");
    Stream<String> openingBracketSeq = Stream.of("[");
    Stream<String> closingBracketSeq = Stream.of("]");

    Stream<String> resultingStream = Seq.ofType(seq, String.class)
      .append(closingBracketSeq)
      .prepend(openingBracketSeq);

    Assert.assertEquals(
      Arrays.asList("[", "foo", "bar", "]"),
      resultingStream.collect(Collectors.toList()));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-merge-streams](https://www.baeldung.com/java-merge-streams)
