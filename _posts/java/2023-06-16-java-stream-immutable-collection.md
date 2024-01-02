---
title: Java 스트림을 불변 컬렉션으로 수집
author: dejavuhyo
date: 2023-06-16 08:30:00 +0900
categories: [Language, Java]
tags: [java-collection, immutable-collection, stream-collection, 자바-컬렉션, 불변-컬렉션]
---

## 1. 표준 자바 사용

### 1) Java의 toUnmodifiableList 사용
Java 10부터 Java의 수집기 클래스에서 toUnmodifiableList 메서드를 사용할 수 있다.

```java
List<String> givenList = Arrays.asList("a", "b", "c");
List<String> result = givenList.stream()
  .collect(toUnmodifiableList());
```

이 메서드를 사용하여 Java의 ImmutableCollections에서 null 값을 지원하지 않는 List 구현을 얻는다.

```java
class java.util.ImmutableCollections$ListN
```

### 2) Java의 collectAndThen 사용
Java의 Collectors 클래스의 collectingAndThen 메소드는 Collector 및 finisher Function을 허용한다. 이 피니셔는 Collector에서 반환된 결과에 적용된다.

```java
List<String> givenList = Arrays.asList("a", "b", "c");
List<String> result = givenList.stream()
  .collect(collectingAndThen(toList(), ImmutableList::copyOf));

System.out.println(result.getClass());
```

이 접근 방식에서는 toCollection Collector를 직접 사용할 수 없기 때문에 요소를 임시 목록으로 수집해야 한다. 그런 다음 불변 목록을 구성한다.

### 3) Stream.toList() 메서드 사용
Java 16에는 `toList()`라는 Stream API의 새로운 메서드가 도입되었다. 이 편리한 메서드는 스트림 요소를 포함하는 수정 불가능한 목록을 반환한다.

```java
@Test
public void whenUsingStreamToList_thenReturnImmutableList() {
    List<String> immutableList = Stream.of("a", "b", "c", "d").toList();
	
    Assertions.assertThrows(UnsupportedOperationException.class, () -> {
        immutableList.add("e");
    });
}
```

단위 테스트에서 볼 수 있듯이 `Stream.toList()`는 변경할 수 없는 목록을 반환한다. 따라서 목록에 새 요소를 추가하려고 하면 [UnsupportedOperationException](https://www.baeldung.com/java-stream-immutable-collection)이 발생한다.

새로운 `Stream.toList()` 메서드는 수정할 수 없는 목록을 반환하므로 기존 `Collectors.toList()` 메서드와 약간 다르다.

## 2. 커스텀 컬렉터 만들기
커스텀 Collector를 구현 하는 옵션도 있다.

### 1) 기본 불변 수집기
이를 달성하기 위해 정적 Collector.of 메소드를 사용할 수 있다.

```java
public static <T> Collector<T, List<T>, List<T>> toImmutableList() {
    return Collector.of(ArrayList::new, List::add,
      (left, right) -> {
        left.addAll(right);
        return left;
      }, Collections::unmodifiableList);
}
```

이 함수를 내장 Collector 처럼 사용할 수 있다.

```java
List<String> givenList = Arrays.asList("a", "b", "c", "d");
List<String> result = givenList.stream()
  .collect(MyImmutableListCollector.toImmutableList());
```

마지막으로 출력 유형을 확인한다.

```java
class java.util.Collections$UnmodifiableRandomAccessList
```

### 2) MyImmutableListCollector 제네릭 만들기
구현에는 한 가지 제한이 있다. 항상 ArrayList가 지원하는 변경 불가능한 인스턴스를 반환한다. 그러나 약간의 개선을 통해 이 수집기가 사용자 지정 유형을 반환하도록 할 수 있다.

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

이제 메서드 구현에서 공급자를 결정하는 대신 사용자에게 공급자를 요청한다.

```java
List<String> givenList = Arrays.asList("a", "b", "c", "d");
List<String> result = givenList.stream()
  .collect(MyImmutableListCollector.toImmutableList(LinkedList::new));
```

또한 ArrayList 대신 LinkedList를 사용하고 있다.

```java
class java.util.Collections$UnmodifiableList
```

이번에는 UnmodifiableRandomAccessList 대신 UnmodifiableList를 얻었다.

## 3. Guava의 수집기 사용
Google Guava 라이브러리를 사용하여 몇 가지 예제를 구동한다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.1-jre</version>
</dependency>
```

Guava 21부터 모든 불변 클래스에는 Java의 표준 Collector 만큼 사용하기 쉬운 Collector가 함께 제공된다.

```java
List<Integer> list = IntStream.range(0, 9)
  .boxed()
  .collect(ImmutableList.toImmutableList());
```

결과 인스턴스는 RegularImmutableList이다.

```java
class com.google.common.collect.RegularImmutableList
```

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-immutable-collection](https://www.baeldung.com/java-stream-immutable-collection)
