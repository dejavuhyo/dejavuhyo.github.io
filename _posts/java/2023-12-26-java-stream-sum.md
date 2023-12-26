---
title: Java 스트림 숫자 합산
author: dejavuhyo
date: 2023-12-26 13:50:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-sum, 자바-스트림, 스트림-합산]
---

## 1. Stream.reduce() 사용
`Stream.reduce()`는 stream의 요소를 축소하는 터미널 작업이다.

스트림의 각 요소에 이진 연산자(누산기)를 적용한다. 여기서 첫 번째 피연산자는 이전 애플리케이션의 반환 값이고 두 번째 피연산자는 현재 스트림 요소이다.

`Reduce()` 메서드를 사용하는 첫 번째 방법에서 Accumulator 함수는 두 개의 정수 값을 더하고 정수 값을 반환하는 람다 식이다.

```java
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
Integer sum = integers.stream()
  .reduce(0, (a, b) -> a + b);
```

같은 방식으로 이미 존재하는 Java 메소드를 사용할 수 있다.

```java
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
Integer sum = integers.stream()
  .reduce(0, Integer::sum);
```

또는 사용자 정의 방법을 정의하고 사용할 수 있다.

```java
public class ArithmeticUtils {

    public static int add(int a, int b) {
        return a + b;
    }
}
```

그런 다음 이 함수를 `Reduce()` 메서드에 매개 변수로 전달할 수 있다.

```java
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
Integer sum = integers.stream()
  .reduce(0, ArithmeticUtils::add);
```

## 2. Stream.collect() 사용
정수 목록의 합계를 계산하는 두 번째 방법은 `Collect()` 터미널 작업을 사용하는 것이다.

```java
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
Integer sum = integers.stream()
  .collect(Collectors.summingInt(Integer::intValue));
```

마찬가지로 Collectors 클래스는 각각 long과 double의 합을 계산하는 `summingLong()` 및 `summingDouble()` 메서드를 제공한다.

## 3. IntStream.sum() 사용
Stream API는 스트림을 IntStream 객체로 변환하는 `mapToInt()` 중간 작업을 제공한다.

이 메소드는 매퍼를 매개변수로 사용하여 변환을 수행한 다음 `sum()` 메소드를 호출하여 스트림 요소의 합계를 계산할 수 있다.

어떻게 사용할 수 있는지에 대한 간단한 예를 살펴본다.

```java
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
Integer sum = integers.stream()
  .mapToInt(Integer::intValue)
  .sum();
```

같은 방식으로 `mapToLong()` 및 `mapToDouble()` 메서드를 사용하여 각각 long과 double의 합을 계산할 수 있다.

## 4. Map과 함께 Stream#sum 사용
`Map<Object, Integer>` 데이터 구조의 값 합계를 계산하려면 먼저 해당 Map의 값에서 스트림을 만든다. 다음으로 이전에 사용한 방법 중 하나를 적용한다.

예를 들어 `IntStream.sum()`을 사용하면 다음과 같다.

```java
Integer sum = map.values()
  .stream()
  .mapToInt(Integer::valueOf)
  .sum();
```

## 5. Objects에 Stream#sum 사용
객체 목록이 있고 이러한 객체의 특정 필드에 대한 모든 값의 합계를 계산하려고 한다고 가정한다.

예제 이다.

```java
public class Item {

    private int id;
    private Integer price;

    public Item(int id, Integer price) {
        this.id = id;
        this.price = price;
    }

    // Standard getters and setters
}
```

다음으로 다음 목록에 있는 모든 항목의 총 가격을 계산한다고 가정한다.

```java
Item item1 = new Item(1, 10);
Item item2 = new Item(2, 15);
Item item3 = new Item(3, 25);
Item item4 = new Item(4, 40);

List<Item> items = Arrays.asList(item1, item2, item3, item4);
```

이 경우 이전 예제에 표시된 메서드를 사용하여 합계를 계산하려면 `map()` 메서드를 호출하여 스트림을 정수 스트림으로 변환 해야 한다.

결과적으로 `Stream.reduce()`, `Stream.collect()` 및 `IntStream.sum()`을 사용하여 합계를 계산할 수 있다.

```java
Integer sum = items.stream()
  .map(x -> x.getPrice())
  .reduce(0, ArithmeticUtils::add);
```

```java
Integer sum = items.stream()
  .map(x -> x.getPrice())
  .reduce(0, Integer::sum);
```

```java
Integer sum = items.stream()
  .map(item -> item.getPrice())
  .reduce(0, (a, b) -> a + b);
```

```java
Integer sum = items.stream()
  .map(x -> x.getPrice())
  .collect(Collectors.summingInt(Integer::intValue));
```

```java
items.stream()
  .mapToInt(x -> x.getPrice())
  .sum();
```

## 6. String과 함께 Stream#sum 사용
일부 정수를 포함하는 String 개체가 있다고 가정한다.

이러한 정수의 합을 계산하려면 먼저 해당 문자열을 배열로 변환해야 한다. 다음으로 정수가 아닌 요소를 필터링하고 마지막으로 해당 배열의 나머지 요소를 숫자로 변환해야 한다.

이러한 모든 단계가 실제로 실행되는 모습이다.

```java
String string = "Item1 10 Item2 25 Item3 30 Item4 45";

Integer sum = Arrays.stream(string.split(" "))
    .filter((s) -> s.matches("\\d+"))
    .mapToInt(Integer::valueOf)
    .sum();
```

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-sum](https://www.baeldung.com/java-stream-sum)
