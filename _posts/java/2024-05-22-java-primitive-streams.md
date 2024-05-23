---
title: Java 기본 스트림
author: dejavuhyo
date: 2024-05-22 10:25:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-primitive, 스트림-기본]
---

## 1. Primitive Streams
스트림은 주로 기본 유형이 아닌 객체 컬렉션과 함께 작동한다.

다행스럽게도 가장 많이 사용되는 세 가지 기본 유형인 `int`, `long` 및 `double`을 사용하는 방법을 제공하기 위해 표준 라이브러리에는 세 가지 기본 유형에 특화된 구현인 `IntStream`, `LongStream` 및 `DoubleStream`이 포함되어 있다.

기본 스트림은 주로 박싱 오버헤드와 다른 기본 형식에 대한 특수 스트림을 생성하는 것이 많은 경우에 그다지 유용하지 않기 때문에 제한된다.

## 2. 산술 연산
`min`, `max`, `sum` 및 `average`와 같이 자주 사용되는 산술 연산에 대한 몇 가지 방법이다.

```java
int[] integers = new int[] {20, 98, 12, 7, 35};
int min = Arrays.stream(integers)
  .min()
  .getAsInt(); // returns 7
```

위의 코드 조각을 단계별로 살펴본다.

`java.util.Arrays.stream(int[])`을 사용하여 IntStream을 생성한 다음 `min()` 메서드를 사용하여 가장 낮은 정수를 `java.util.OptionalInt`로 가져오고 마지막으로 `getAsInt()`를 호출하여 int 값을 가져왔다.

IntStream을 생성하는 또 다른 방법은 `IntStream.of(int…)`를 사용하는 것이다. `max()` 메소드는 가장 큰 정수를 반환한다.

```java
int max = IntStream.of(20, 98, 12, 7, 35)
  .max()
  .getAsInt(); // returns 98
```

정수의 합을 얻으려면 `sum()` 메소드를 호출하기만 하면 되고 `getAsInt()`는 이미 결과를 int 값으로 반환하므로 사용할 필요가 없다.

```java
int sum = IntStream.of(20, 98, 12, 7, 35).sum(); // returns 172
```

정수 값의 평균을 얻기 위해 `average()` 메소드를 호출하고, double 유형의 값을 반환하므로 `getAsDouble()`을 사용해야 한다.

```java
double avg = IntStream.of(20, 98, 12, 7, 35)
  .average()
  .getAsDouble(); // returns 34.4
```

## 3. Range
범위를 기반으로 IntStream을 만들 수도 있다.

```java
int sum = IntStream.range(1, 10)
  .sum(); // returns 45
int sum = IntStream.rangeClosed(1, 10)
  .sum(); // returns 55
```

위의 코드 조각에서 볼 수 있듯이 정수 값 `range()` 및 `rangeClosed()`의 범위를 만드는 두 가지 방법이 있다.

차이점은 `range()`의 끝은 배타적이지만 `rangeClosed()`에는 포함된다는 것이다.

Range 메서드는 `IntStream` 및 `LongStream`에만 사용할 수 있다.

`for-each` 루프의 멋진 형태로 range를 사용할 수 있다.

```java
IntStream.rangeClosed(1, 5)
  .forEach(System.out::println);
```

이를 `for-each` 루프 교체로 사용하면 병렬 실행도 활용할 수 있다는 장점이 있다.

```java
IntStream.rangeClosed(1, 5)
  .parallel()
  .forEach(System.out::println);
```

이러한 멋진 루프가 유용하기는 하지만 단순성, 가독성 및 일부 경우 성능 때문에 간단한 반복을 위해 기능적 루프 대신 기존 for 루프를 사용하는 것이 더 좋다.

## 4. Boxing과 Unboxing
기본 값을 래퍼 값으로 변환해야 할 때가 있다.

이러한 경우 `boxed()` 메서드를 사용할 수 있다.

```java
List<Integer> evenInts = IntStream.rangeClosed(1, 10)
  .filter(i -> i % 2 == 0)
  .boxed()
  .collect(Collectors.toList());
```

래퍼 클래스 스트림에서 기본 스트림으로 변환할 수도 있다.

```java
// returns 78
int sum = Arrays.asList(33,45)
  .stream()
  .mapToInt(i -> i)
  .sum();
```

기본 스트림을 생성하려면 언제든지 mapToXxx 및 flatMapToXxx 메서드를 사용할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-8-primitive-streams](https://www.baeldung.com/java-8-primitive-streams)
