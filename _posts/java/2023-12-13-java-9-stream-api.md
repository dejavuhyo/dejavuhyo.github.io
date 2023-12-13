---
title: Java 9 스트림 API 개선
author: dejavuhyo
date: 2023-12-13 22:00:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-api, 자바-스트림, 스트림-api]
---

## 1. Stream Takewhile/Dropwhile
이러한 메서드에 대한 토론은 StackOverflow에서 반복적으로 나타났다(가장 인기 있는 것은 이 메서드 이다).

이 스트림의 현재 값 길이가 10보다 작아질 때까지 이전 스트림의 값에 문자 하나를 추가하여 String의 스트림을 생성한다고 가정한다.

Java 8에서는 실제로 다른 목적으로 사용되는 Limit, allMatch와 같은 짧은 순환 중간 작업 중 하나를 사용하거나 Spliterator를 기반으로 자체 takeWhile 구현을 작성하여 간단한 문제를 복잡하게 만들 수 있다.

Java 9를 사용하면 솔루션이 쉽다.

```java
Stream<String> stream = Stream.iterate("", s -> s + "s")
  .takeWhile(s -> s.length() < 10);
```

takeWhile 작업은 이러한 요소의 가장 긴 접두사(스트림이 정렬된 경우) 또는 스트림 요소의 하위 집합(스트림이 정렬되지 않은 경우)을 결정하기 위해 요소에 적용되는 조건자를 사용한다.

앞으로 나아가려면 "가장 긴 접두사"와 "스트림의 하위 집합"이라는 용어가 무엇을 의미하는지 더 잘 이해해야 한다.

* 가장 긴 접두사는 주어진 술어와 일치하는 스트림 요소의 연속 시퀀스이다. 시퀀스의 첫 번째 요소는 이 스트림의 첫 번째 요소이며 시퀀스의 마지막 요소 바로 다음 요소는 지정된 조건자와 일치하지 않는다.

* 스트림의 하위 집합은 주어진 조건과 일치하는 스트림의 일부(전부는 아님) 요소 집합이다.

이러한 주요 용어를 소개한 후에는 또 다른 새로운 dropWhile 작업을 쉽게 이해할 수 있다.

takeWhile과 정반대이다. 스트림이 정렬된 경우 dropWile은 주어진 조건과 일치하는 요소의 가장 긴 접두사를 삭제한 후 이 스트림의 나머지 요소로 구성된 스트림을 반환한다.

그렇지 않고 스트림이 순서가 지정되지 않은 경우 dropWile은 주어진 조건과 일치하는 요소의 하위 집합을 삭제한 후 이 스트림의 나머지 요소로 구성된 스트림을 반환한다.

이전에 얻은 스트림을 사용하여 처음 5개 요소를 버린다.

```java
stream.dropWhile(s -> !s.contains("sssss"));
```

간단히 말해서, dropWhile 작업은 요소에 대해 지정된 조건자가 true를 반환하고 첫 번째 조건자의 false 제거를 중지하는 동안 요소를 제거한다.

## 2. Stream Iterate
유한 스트림 생성을 위한 오버로드된 반복 방법이다. 일부 함수에 의해 생성된 무한 순차적 스트림을 반환하는 [finite](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Stream.html#iterate(T,java.util.function.UnaryOperator)) 변형과 ​​혼동하지 않는다.

새로운 반복에서는 스트림을 종료해야 하는 시기를 결정하기 위해 요소에 적용되는 조건자를 추가하여 이 메서드를 약간 수정한다. 사용법은 매우 편리하고 간결하다.

```java
Stream.iterate(0, i -> i < 10, i -> i + 1)
  .forEach(System.out::println);
```

해당 for문과 연결할 수 있다.

```java
for (int i = 0; i < 10; ++i) {
    System.out.println(i);
}
```

## 3. Stream Ofnullable
스트림에 요소를 넣어야 하는 상황이 있다. 때때로 이 요소는 null 일 수 있지만 스트림에 그러한 값이 포함되는 것을 원하지 않는다. 요소가 null인지 확인하는 if문이나 삼항 연산자를 작성하게 된다.

컬렉션 및 맵 변수가 성공적으로 생성되고 채워졌다고 가정하고 다음 예를 살펴본다.

```java
collection.stream()
  .flatMap(s -> {
      Integer temp = map.get(s);
      return temp != null ? Stream.of(temp) : Stream.empty();
  })
  .collect(Collectors.toList());
```

이러한 상용구 코드를 방지하기 위해 ofNullable 메서드가 스트림 클래스에 추가되었다. 이 방법을 사용하면 이전 샘플을 다음과 같이 간단하게 변환할 수 있다.

```java
collection.stream()
  .flatMap(s -> Stream.ofNullable(map.get(s)))
  .collect(Collectors.toList());
```

## [출처 및 참고]
* [https://www.baeldung.com/java-9-stream-api](https://www.baeldung.com/java-9-stream-api)
