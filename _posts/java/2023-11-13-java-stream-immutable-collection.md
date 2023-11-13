---
title: Java Stream Immutable Collection
author: dejavuhyo
date: 2023-11-13 22:30:00 +0900
categories: [Language, Java]
tags: [java-stream, java-collection, immutable-collection, 자바-스트림, 자바-컬렉션, 불변-컬렉션]
---

## 1. 표준 자바 사용

### 1) Java의 toUnmodifyingList 사용
Java 10부터 Java의 Collectors 클래스에서 toUnmodifyingList 메서드를 사용할 수 있다.

```java
List<String> givenList = Arrays.asList("a", "b", "c");
List<String> result = givenList.stream()
  .collect(toUnmodifiableList());
```

이 메서드를 사용하면 Java의 ImmutableCollections에서 null 값을 지원하지 않는 List 구현을 얻을 수 있다.

```java
class java.util.ImmutableCollections$ListN
```

### 2) Java의 collectionAndThen 사용
Java의 Collectors 클래스의 presentsAndThen 메소드는 Collector 및 finisher Function을 허용한다. 이 마무리 도구는 Collector에서 반환된 결과에 적용된다.

```java
List<String> givenList = Arrays.asList("a", "b", "c");
List<String> result = givenList.stream()
  .collect(collectingAndThen(toList(), ImmutableList::copyOf));

System.out.println(result.getClass());
```

이 접근 방식을 사용하면 toCollection 수집기를 직접 사용할 수 없으므로 요소를 임시 목록으로 수집해야 한다. 그런 다음 그것으로부터 불변 목록을 구성한다.

### 3) Stream.toList() 메서드 사용
Java 16에는 Stream API에 `toList()`라는 새로운 메서드가 도입되었다. 이 편리한 메소드는 스트림 요소를 포함하는 수정 불가능한 목록을 반환한다.

```java
@Test
public void whenUsingStreamToList_thenReturnImmutableList() {
    List<String> immutableList = Stream.of("a", "b", "c", "d").toList();

    Assertions.assertThrows(UnsupportedOperationException.class, () -> {
        immutableList.add("e");
    });
}
```

단위 테스트에서 볼 수 있듯이 `Stream.toList()`는 불변 목록을 반환한다. 따라서 목록에 새 요소를 추가하려고 하면 단순히 UnsupportedOperationException이 발생한다.

새로운 `Stream.toList()` 메서드는 수정 불가능한 목록을 반환하므로 기존 `Collectors.toList()`와 약간 다르다.

## 2. 맞춤형 수집기 구축
사용자 정의 Collector를 구현할 수 있는 옵션도 있다.

### 1) 기본 불변 수집기
이를 달성하기 위해 정적 `Collector.of` 메소드를 사용할 수 있다.

```java
public static <T> Collector<T, List<T>, List<T>> toImmutableList() {
    return Collector.of(ArrayList::new, List::add,
      (left, right) -> {
        left.addAll(right);
        return left;
      }, Collections::unmodifiableList);
}
```

이 함수는 내장된 Collector 처럼 사용할 수 있다.

```java
List<String> givenList = Arrays.asList("a", "b", "c", "d");
List<String> result = givenList.stream()
  .collect(MyImmutableListCollector.toImmutableList());
```

마지막으로 출력 유형을 확인해 본다.

```java
class java.util.Collections$UnmodifiableRandomAccessList
```

### 2) MyImmutableListCollector를 일반화 하기
구현에는 한 가지 제한 사항이 있다. 즉, 항상 ArrayList가 지원하는 불변 인스턴스를 반환한다. 그러나 약간의 개선을 통해 이 수집기가 사용자 지정 유형을 반환하도록 만들 수 있다.

```java
public static <T, A extends List<T>> Collector<T, A, List<T>> toImmutableList(
  Supplier<A> supplier) {
 
    return Collector.of(
      supplier,
      List::add, (left, right) -> {
        left.addAll(right);
        return left;
      }, Collections::unmodifiableList);
}
```

이제 메소드 구현에서 공급자를 결정하는 대신 사용자에게 공급자를 요청한다.

```java
List<String> givenList = Arrays.asList("a", "b", "c", "d");
List<String> result = givenList.stream()
  .collect(MyImmutableListCollector.toImmutableList(LinkedList::new));
```

또한 ArrayList 대신 LinkedList를 사용하고 있다.

```java
class java.util.Collections$UnmodifiableList
```

이번에는 UnmodifyingRandomAccessList 대신 UnmodifyingList를 얻었다.

## 3. Guava의 Collector 사용 하기
Google Guava 라이브러리를 사용하여 몇 가지 예제를 구동해 본다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.1-jre</version>
</dependency>
```

Guava 21부터 모든 불변 클래스에는 Java의 표준 Collector 처럼 사용하기 쉬운 Collector가 함께 제공된다.

```java
List<Integer> list = IntStream.range(0, 9)
  .boxed()
  .collect(ImmutableList.toImmutableList());
```

결과 인스턴스는 RegularImmutableList 이다.

```java
class com.google.common.collect.RegularImmutableList
```

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-immutable-collection](https://www.baeldung.com/java-stream-immutable-collection)
