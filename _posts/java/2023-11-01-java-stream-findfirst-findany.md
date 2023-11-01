---
title: Java 8 Stream findFirst() 대 findAny()
author: dejavuhyo
date: 2023-11-01 21:20:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-findfirst, stream-findany, 자바-스트림, 스트림]
---

## 1. Stream.findAny() 사용
이름에서 알 수 있듯이 `findAny()` 메서드를 사용하면 Stream에서 모든 요소를 ​​찾을 수 있다. 발생 순서에 주의를 기울이지 않고 요소를 찾을 때 이를 사용한다.

이 메서드는 Stream이 비어 있으면 비어 있는 Optional 인스턴스를 반환한다.

```java
@Test
public void createStream_whenFindAnyResultIsPresent_thenCorrect() {
    List<String> list = Arrays.asList("A","B","C","D");

    Optional<String> result = list.stream().findAny();

    assertTrue(result.isPresent());
    assertThat(result.get(), anyOf(is("A"), is("B"), is("C"), is("D")));
}
```

비병렬 작업에서는 Stream의 첫 번째 요소를 반환할 가능성이 높지만 이에 대한 보장은 없다.

병렬 작업을 처리할 때 최대 성능을 얻으려면 결과를 안정적으로 결정할 수 없다.

```java
@Test
public void createParallelStream_whenFindAnyResultIsPresent_thenCorrect()() {
    List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
    Optional<Integer> result = list
      .stream().parallel()
      .filter(num -> num < 4).findAny();

    assertTrue(result.isPresent());
    assertThat(result.get(), anyOf(is(1), is(2), is(3)));
}
```

## 2. Stream.findFirst() 사용
`findFirst()` 메소드는 Stream의 첫 번째 요소를 찾는다. 따라서 시퀀스의 첫 번째 요소를 구체적으로 원할 때 이 방법을 사용한다.

만남 순서가 없으면 Stream의 모든 요소를 ​​반환 한다. `java.util.streams` 패키지 문서에 따르면, "스트림은 정의된 발생 순서를 가질 수도 있고 그렇지 않을 수도 있다. 소스와 중간 작업에 따라 다르다."

반환 유형은 Stream이 비어 있으면 비어 있는 Optional 인스턴스이기도 한다.

```java
@Test
public void createStream_whenFindFirstResultIsPresent_thenCorrect() {

    List<String> list = Arrays.asList("A", "B", "C", "D");

    Optional<String> result = list.stream().findFirst();

    assertTrue(result.isPresent());
    assertThat(result.get(), is("A"));
}
```

findFirst 메소드의 동작은 병렬 시나리오에서 변경되지 않는다. 만남 순서가 존재하는 경우 항상 결정론적으로 동작한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-findfirst-vs-findany](https://www.baeldung.com/java-stream-findfirst-vs-findany)
