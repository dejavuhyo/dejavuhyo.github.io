---
title: Java 무한 스트림
author: dejavuhyo
date: 2023-12-06 21:55:00 +0900
categories: [Language, Java]
tags: [java-stream, inifinite-streams, 자바-스트림, 무한-스트림]
---

## 1. Intermediate 및 Terminal Operations
모든 스트림 작업은 중간 작업과 터미널 작업으로 구분되며 결합되어 스트림 파이프라인을 형성한다.

스트림 파이프라인은 소스(예: Collection, array, generator function, I/O 채널 또는 무한 시퀀스 생성기)로 구성된다. 0개 이상의 중간 작업과 터미널 작업이 이어진다.

### 1) Intermediate Operations
일부 터미널 작업이 호출될 때까지 중간 작업은 실행되지 않는다.

이들은 스트림 실행의 파이프라인을 형성하도록 구성된다. 중간 작업은 다음 방법으로 스트림 파이프라인에 추가될 수 있다.

* `filter()`

* `map()`

* `flatMap()`

* `distinct()`

* `sorted()`

* `peek()`

* `limit()`

* `skip()`

모든 중간 작업은 게으른 작업이므로 처리 결과가 실제로 필요할 때까지 실행되지 않는다.

기본적으로 중간 작업은 새 스트림을 반환한다. 중간 작업을 실행하면 실제로 어떤 작업도 수행되지 않지만 대신 탐색할 때 주어진 조건과 일치하는 초기 스트림의 요소를 포함하는 새 스트림이 생성된다.

따라서 스트림 순회는 파이프라인의 터미널 작업이 실행될 때까지 시작되지 않는다.

이는 매우 중요한 속성이며 특히 무한 스트림에 중요하다. 이를 통해 터미널 작업이 호출될 때만 실제로 호출되는 스트림을 생성할 수 있기 때문이다.

### 2) Terminal Operations
터미널 작업은 스트림을 통과하여 결과나 부작용을 생성할 수 있다.

터미널 작업이 수행된 후에는 스트림 파이프라인이 소비된 것으로 간주되어 더 이상 사용할 수 없다. 거의 모든 경우에 터미널 작업은 반환되기 전에 데이터 소스 탐색과 파이프라인 처리를 완료하여 열심히 작업한다.

무한 스트림과 관련하여 터미널 작업의 열의는 중요하다. 처리하는 순간 스트림이 예를 들어 `Limit()` 변환으로 적절하게 제한되어 있는지 신중하게 생각해야 하기 때문이다. 터미널 작업은 다음과 같다.

* `forEach()`

* `forEachOrdered()`

* `toArray()`

* `reduce()`

* `collect()`

* `min()`

* `max()`

* `count()`

* `anyMatch()`

* `allMatch()`

* `noneMatch()`

* `findFirst()`

* `findAny()`

이러한 각 작업은 모든 중간 작업의 실행을 트리거한다.

## 2. 무한 스트림
스트림의 게으름을 활용하는 무한 스트림을 작성할 수 있다.

0부터 2만큼 증가하는 요소의 무한 스트림을 생성한다고 가정해 본다. 그런 다음 터미널 작업을 호출하기 전에 해당 시퀀스를 제한해야 한다.

터미널 작업인 `Collect()` 메서드를 실행하기 전에 `Limit()` 메서드를 사용하는 것이 중요하다. 그렇지 않으면 프로그램이 무기한 실행된다.

```java
// given
Stream<Integer> infiniteStream = Stream.iterate(0, i -> i + 2);

// when
List<Integer> collect = infiniteStream
  .limit(10)
  .collect(Collectors.toList());

// then
assertEquals(collect, Arrays.asList(0, 2, 4, 6, 8, 10, 12, 14, 16, 18));
```

`iterate()` 메소드를 사용하여 무한 스트림을 생성했다. 그런 다음 `Limit()` 변환과 `Collect()` 터미널 작업을 호출했다. 그런 다음 결과 목록에는 스트림의 게으름으로 인해 무한 시퀀스의 처음 10개 요소가 포함된다.

## 3. 맞춤형 요소 유형의 무한 스트림
무작위 UUID 의 무한 스트림을 생성한다고 가정해 본다.

Stream API를 사용하여 이를 달성하는 첫 번째 단계는 다음과 같은 임의 값의 공급자를 만드는 것이다.

```java
Supplier<UUID> randomUUIDSupplier = UUID::randomUUID;
```

공급자를 정의할 때 `generate()` 메서드를 사용하여 무한 스트림을 생성할 수 있다.

```java
Stream<UUID> infiniteStreamOfRandomUUID = Stream.generate(randomUUIDSupplier);
```

그런 다음 해당 스트림에서 몇 가지 요소를 가져올 수 있다. 프로그램을 유한한 시간 내에 끝내려면 `Limit()` 메서드를 사용해야 한다.

```java
List<UUID> randomInts = infiniteStreamOfRandomUUID
  .skip(10)
  .limit(10)
  .collect(Collectors.toList());
```

`Skip()` 변환을 사용하여 처음 10개의 결과를 버리고 다음 10개의 요소를 가져온다. Stream의 `generate()` 메소드에 공급자 인터페이스의 함수를 전달하여 사용자 정의 유형 요소의 무한 스트림을 생성할 수 있다.

## 4. Do-While 스트림 방식
코드에 간단한 do..while 루프가 있다고 가정해 본다.

```java
int i = 0;
while (i < 10) {
    System.out.println(i);
    i++;
}
```

i counter를 10번 인쇄하고 있다. 이러한 구성은 Stream API를 사용하여 쉽게 작성할 수 있으며 이상적으로는 스트림에 `doWhile()` 메서드를 사용하는 것이 좋다.

불행하게도 스트림에는 그러한 메서드가 없으며 표준 `do-while` 루프와 유사한 기능을 달성하려면 `Limit()` 메서드를 사용해야 한다.

```java
Stream<Integer> integers = Stream
  .iterate(0, i -> i + 1);
integers
  .limit(10)
  .forEach(System.out::println);
```

더 적은 코드로 명령형 while 루프와 같은 기능을 달성했지만, stream 객체에 `doWhile()` 메서드가 있는 경우처럼 `Limit()` 함수 호출이 설명적이지 않다.

## [출처 및 참고]
* [https://www.baeldung.com/java-inifinite-streams](https://www.baeldung.com/java-inifinite-streams)
