---
title: Java 스트림 반복
author: dejavuhyo
date: 2023-11-30 21:00:00 +0900
categories: [Language, Java]
tags: [java-stream, iterable-stream, 자바-스트림, 스트림-반복]
---

## 1. Iterable을 스트림 으로 변환
Iterable 인터페이스는 일반성을 염두에 두고 설계되었으며 자체적으로 `stream()` 메서드를 제공 하지 않는다.

간단히 말해서 이를 `StreamSupport.stream()` 메서드에 전달하고 지정된 Iterable 인스턴스에서 Stream을 가져올 수 있다.

Iterable 인스턴스를 고려해 본다.

```java
Iterable<String> iterable = Arrays.asList("Testing", "Iterable", "conversion", "to", "Stream");
```

이 Iterable 인스턴스를 스트림으로 변환하는 방법은 다음과 같다.

```java
StreamSupport.stream(iterable.spliterator(), false);
```

`StreamSupport.stream()`의 두 번째 매개변수는 결과 스트림이 병렬이어야 하는지 또는 순차적이어야 하는지를 결정한다. 병렬 Stream의 경우 true로 설정해야 한다.

이제 구현을 테스트해 본다.

```java
@Test
public void givenIterable_whenConvertedToStream_thenNotNull() {
    Iterable<String> iterable = Arrays.asList("Testing", "Iterable", "conversion", "to", "Stream");
    Assert.assertNotNull(StreamSupport.stream(iterable.spliterator(), false));
}
```

또한 간단한 참고 사항은 스트림은 재사용할 수 없지만 Iterable은 재사용 가능하다는 것이다. 또한 주어진 Iterable에 의해 설명된 요소에 대해 `java.lang.Spliterator` 인스턴스를 반환하는 `Spliterator()` 메서드를 제공한다.

## 2. 스트림 작업 수행
간단한 스트림 작업을 수행해 본다.

```java
@Test
public void whenConvertedToList_thenCorrect() {
    Iterable<String> iterable = Arrays.asList("Testing", "Iterable", "conversion", "to", "Stream");

    List<String> result = StreamSupport.stream(iterable.spliterator(), false)
      .map(String::toUpperCase)
      .collect(Collectors.toList());

    assertThat(result, contains("TESTING", "ITERABLE", "CONVERSION", "TO", "STREAM"));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-iterable-to-stream](https://www.baeldung.com/java-iterable-to-stream)
