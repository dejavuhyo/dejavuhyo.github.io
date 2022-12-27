---
title: Java 스트림 최종 연산
author: dejavuhyo
date: 2022-10-05 09:20:00 +0900
categories: [Language, Java]
tags: [java-stream-terminal, terminal-operation, java-stream, stream-terminal, stream-terminal-operation, stream, 자바-스트림-최종-연산, 스트림-최종-연산, 최종-연산, 자바-스트림, 스트림]
---

## 1. 스트림의 최종 연산(terminal operation)
스트림 API에서 중개 연산을 통해 변환된 스트림은 마지막으로 최종 연산을 통해 각 요소를 소모하여 결과를 표시한다.

즉, 지연(lazy)되었던 모든 중개 연산들이 최종 연산 시에 모두 수행되는 것이다. 이렇게 최종 연산 시에 모든 요소를 소모한 해당 스트림은 더는 사용할 수 없게 된다.

스트림 API에서 사용할 수 있는 대표적인 최종 연산과 그에 따른 메소드는 다음과 같다.

* 요소의 출력: `forEach()`

* 요소의 소모: `reduce()`

* 요소의 검색: `findFirst()`, `findAny()`

* 요소의 검사: `anyMatch()`, `allMatch()`, `noneMatch()`

* 요소의 통계: `count()`, `min()`, `max()`

* 요소의 연산: `sum()`, `average()`

* 요소의 수집: `collect()`

## 2. 요소의 출력
앞선 수업에서 자주 사용한 `forEach()` 메소드는 스트림의 각 요소를 소모하여 명시된 동작을 수행한다. 반환 타입이 void이므로 보통 스트림의 모든 요소를 출력하는 용도로 많이 사용한다.

* 예제

```java
Stream<String> stream = Stream.of("넷", "둘", "셋", "하나");
stream.forEach(System.out::println);
```

* 실행 결과

```text
넷
둘
셋
하나
```

## 3. 요소의 소모
스트림의 최종 연산은 모두 스트림의 각 요소를 소모하여 연산을 수행하게 된다.

하지만 `reduce()` 메소드는 첫 번째와 두 번째 요소를 가지고 연산을 수행한 뒤, 그 결과와 세 번째 요소를 가지고 또다시 연산을 수행한다. 이런 식으로 해당 스트림의 모든 요소를 소모하여 연산을 수행하고, 그 결과를 반환하게 된다.

또한, 인수로 초깃값을 전달하면 초깃값과 해당 스트림의 첫 번째 요소와 연산을 시작하며, 그 결과와 두 번째 요소를 가지고 계속해서 연산을 수행하게 된다.

다음 예제는 스트림의 각 문자열 요소를 `++` 기호로 연결하여 출력하는 예제이다.

* 예제

```java
Stream<String> stream1 = Stream.of("넷", "둘", "셋", "하나");
Stream<String> stream2 = Stream.of("넷", "둘", "셋", "하나");

Optional<String> result1 = stream1.reduce((s1, s2) -> s1 + "++" + s2);
result1.ifPresent(System.out::println);

String result2 = stream2.reduce("시작", (s1, s2) -> s1 + "++" + s2);
System.out.println(result2);
```

* 실행 결과

```text
넷++둘++셋++하나
시작++넷++둘++셋++하나
```

위의 예제처럼 인수로 초깃값을 전달하는 `reduce()` 메소드의 반환 타입은 `Optional<T>`가 아닌 T 타입이다. 그 이유는 비어 있는 스트림과 reduce 연산을 할 경우 전달받은 초깃값을 그대로 반환해야 하기 때문이다.

## 4. 요소의 검색
`findFirst()`와 `findAny()` 메소드는 해당 스트림에서 첫 번째 요소를 참조하는 Optional 객체를 반환한다. 두 메소드 모두 비어 있는 스트림에서는 비어있는 Optional 객체를 반환한다.

다음 예제는 스트림의 모든 요소를 정렬한 후, 첫 번째에 위치한 요소를 출력하는 예제이다.

* 예제

```java
IntStream stream1 = IntStream.of(4, 2, 7, 3, 5, 1, 6);
IntStream stream2 = IntStream.of(4, 2, 7, 3, 5, 1, 6);
OptionalInt result1 = stream1.sorted().findFirst();
System.out.println(result1.getAsInt());

OptionalInt result2 = stream2.sorted().findAny();
System.out.println(result2.getAsInt());
```

* 실행 결과

```text
1
1
```

위의 예제에서 볼 수 있듯이 두 메소드의 결과는 같게 출력된다. 하지만 병렬 스트림인 경우에는 `findAny()` 메소드를 사용해야만 정확한 연산 결과를 반환할 수 있다.

## 5. 요소의 검사
해당 스트림의 요소 중에서 특정 조건을 만족하는 요소가 있는지, 아니면 모두 만족하거나 모두 만족하지 않는지를 다음 메소드를 사용하여 확인할 수 있다.

* `anyMatch()`: 해당 스트림의 일부 요소가 특정 조건을 만족할 경우에 true를 반환함

* `allMatch()`: 해당 스트림의 모든 요소가 특정 조건을 만족할 경우에 true를 반환함

* `noneMatch()`: 해당 스트림의 모든 요소가 특정 조건을 만족하지 않을 경우에 true를 반환함

세 메소드 모두 인수로 Predicate 객체를 전달받으며, 요소의 검사 결과는 boolean 값으로 반환한다.

다음 예제는 스트림의 모든 요소를 검사하여 80보다 큰 값을 가지는 요소가 하나라도 존재하는지를 검사하는 예제이다.

* 예제

```java
IntStream stream1 = IntStream.of(30, 90, 70, 10);
IntStream stream2 = IntStream.of(30, 90, 70, 10);

System.out.println(stream1.anyMatch(n -> n > 80));
System.out.println(stream2.allMatch(n -> n > 80));
```

* 실행 결과

```text
true
false
```

## 6. 요소의 통계: count(), min(), max()
`count()` 메소드는 해당 스트림의 요소의 총 개수를 long 타입의 값으로 반환한다.

또한, `max()`와 `min()` 메소드를 사용하면 해당 스트림의 요소 중에서 가장 큰 값과 가장 작은 값을 가지는 요소를 참조하는 Optional 객체를 얻을 수 있다.

* 예제

```java
IntStream stream1 = IntStream.of(30, 90, 70, 10);
IntStream stream2 = IntStream.of(30, 90, 70, 10);

System.out.println(stream1.count());
System.out.println(stream2.max().getAsInt());
```

* 실행 결과

```text
4
90
```

## 7. 요소의 연산: sum(), average()
IntStream이나 DoubleStream과 같은 기본 타입 스트림에는 해당 스트림의 모든 요소에 대해 합과 평균을 구할 수 있는 `sum()`과 `average()` 메소드가 각각 정의되어 있다.

이때 `average()` 메소드는 각 기본 타입으로 래핑 된 Optional 객체를 반환한다.

* 예제

```java
IntStream stream1 = IntStream.of(30, 90, 70, 10);
DoubleStream stream2 = DoubleStream.of(30.3, 90.9, 70.7, 10.1);

System.out.println(stream1.sum());
System.out.println(stream2.average().getAsDouble());
```

* 실행 결과

```text
200
50.5
```

## 8. 요소의 수집
collect() 메소드는 인수로 전달되는 Collectors 객체에 구현된 방법대로 스트림의 요소를 수집한다. 또한, Collectors 클래스에는 미리 정의된 다양한 방법이 클래스 메소드로 정의되어 있다.

이 외에도 사용자가 직접 Collector 인터페이스를 구현하여 자신만의 수집 방법을 정의할 수도 있다. 스트림 요소의 수집 용도별 사용할 수 있는 Collectors 메소드는 다음과 같다.

* 스트림을 배열이나 컬렉션으로 변환: `toArray()`, `toCollection()`, `toList()`, `toSet()`, `toMap()`

* 요소의 통계와 연산 메소드와 같은 동작을 수행: `counting()`, `maxBy()`, `minBy()`, `summingInt()`, `averagingInt()` 등

* 요소의 소모와 같은 동작을 수행: `reducing()`, `joining()`

* 요소의 그룹화와 분할: `groupingBy()`, `partitioningBy()`

다음 예제는 `collect()` 메소드를 이용하여 해당 스트림을 리스트로 변환하는 예제이다.

* 예제

```java
Stream<String> stream = Stream.of("넷", "둘", "하나", "셋");

List<String> list = stream.collect(Collectors.toList());
Iterator<String> iter = list.iterator();
while(iter.hasNext()) {
    System.out.print(iter.next() + " ");
}
```

* 실행 결과

```text
넷 둘 하나 셋
```

다음 예제는 Collectors 클래스의 `partitioningBy()` 메소드를 이용하여 해당 스트림의 각 요소별 글자 수에 따라 홀수와 짝수로 나누어 저장하는 예제이다.

* 예제

```java
Stream<String> stream = Stream.of("HTML", "CSS", "JAVA", "PHP");

Map<Boolean, List<String>> patition = stream.collect(Collectors.partitioningBy(s -> (s.length() % 2) == 0));

List<String> oddLengthList = patition.get(false);
System.out.println(oddLengthList);

List<String> evenLengthList = patition.get(true);
System.out.println(evenLengthList);
```

* 실행 결과

```text
[CSS, PHP]
[HTML, JAVA]
```

## 9. 대표적인 최종 연산 메소드
스트림 API에서 사용할 수 있는 대표적인 최종 연산을 위한 메소드는 다음과 같다.

| 메소드 | 설명 |
|:-----:|:-----:|
| `void forEach(Consumer<? super T> action)` | 스트림의 각 요소에 대해 해당 요소를 소모하여 명시된 동작을 수행함 |
| `Optional<T> reduce(BinaryOperator<T> accumulator)` `T reduce(T identity, BinaryOperator<T> accumulator)` | 처음 두 요소를 가지고 연산을 수행한 뒤, 그 결과와 다음 요소를 가지고 또다시 연산을 수행함. 이런 식으로 해당 스트림의 모든 요소를 소모하여 연산을 수행하고, 그 결과를 반환함 |
| `Optional<T> findFirst()`, `Optional<T> findAny()` | 해당 스트림에서 첫 번째 요소를 참조하는 Optional 객체를 반환함. (`findAny()` 메소드는 병렬 스트림일 때 사용함) |
| `boolean anyMatch(Predicate<? super T> predicate)` | 해당 스트림의 일부 요소가 특정 조건을 만족할 경우에 true를 반환함 |
| `boolean allMatch(Predicate<? super T> predicate)` | 해당 스트림의 모든 요소가 특정 조건을 만족할 경우에 true를 반환함 |
| `boolean noneMatch(Predicate<? super T> predicate)` | 해당 스트림의 모든 요소가 특정 조건을 만족하지 않을 경우에 true를 반환함 |
| `long count()` | 해당 스트림의 요소의 개수를 반환함 |
| `Optional<T> max(Comparator<? super T> comparator)` | 해당 스트림의 요소 중에서 가장 큰 값을 가지는 요소를 참조하는 Optional 객체를 반환함 |
| `Optional<T> min(Comparator<? super T> comparator)` | 해당 스트림의 요소 중에서 가장 작은 값을 가지는 요소를 참조하는 Optional 객체를 반환함 |
| `T sum()` | 해당 스트림의 모든 요소에 대해 합을 구하여 반환함 |
| `Optional<T> average()` | 해당 스트림의 모든 요소에 대해 평균값을 구하여 반환함 |
| `<R,A> R collect(Collector<? super T,A,R> collector)` | 인수로 전달되는 Collectors 객체에 구현된 방법대로 스트림의 요소를 수집함 |

## [출처 및 참고]
* <http://www.tcpschool.com/java/java_stream_terminal>
