---
title: Java Iterator 크기
author: dejavuhyo
date: 2023-06-26 08:55:00 +0900
categories: [Language, Java]
tags: [java-collection, collection-remove-elements, remove-elements, 자바-컬렉션, 컬렉션-요소-제거, 요소-제거]
---

## 1. 컬렉션 정의
원래 데이터 구조를 변경하는 두 가지 방식이 있다. 항목을 제거하는 대신 항목 없이 원래 컬렉션의 복사본을 만드는 두 가지 다른 옵션이 있다.

예제 전체에서 다음 컬렉션을 사용하고 다른 방법을 사용하여 동일한 결과를 얻을 수 있다.

```java
Collection<String> names = new ArrayList<>();
names.add("John");
names.add("Ana");
names.add("Mary");
names.add("Anthony");
names.add("Mark");
```

## 2. Iterator로 요소 제거
Java의 Iterator를 사용하면 [Collection](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collection.html) 내의 모든 개별 요소를 탐색하고 제거할 수 있다.

먼저 [반복자](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collection.html#iterator()) 메서드를 사용하여 해당 요소에 대해 반복자를 검색해야 한다. 그런 다음 [next](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Iterator.html#next())의 도움으로 각 요소를 방문 하고 [remove](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Iterator.html#remove())를 사용하여 제거 할 수 있다.

```java
Iterator<String> i = names.iterator();

while(i.hasNext()) {
    String e = i.next();
    if (e.startsWith("A")) {
        i.remove();
    }
}
```

단순함에도 불구하고 고려해야 할 몇 가지 주의 사항이 있다.

* 컬렉션에 따라 ConcurrentModificationException 예외가 발생할 수 있다.

* 요소를 제거하기 전에 요소를 반복해야 한다.

* 컬렉션에 따라 제거가 예상과 다르게 작동할 수 있다. 예: `ArrayList.Iterator`는 컬렉션에서 요소를 제거하고 후속 데이터를 왼쪽으로 이동하는 반면 `LinkedList.Iterator`는 단순히 다음 요소에 대한 포인터를 조정한다. 따라서 `LinkedList.Iterator`는 항목을 제거할 때 `ArrayList.Iterator` 보다 훨씬 더 잘 수행된다.

## 3. 자바 8 및 Collection.removeIf()
Java 8 에서는 [Predicate](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/function/Predicate.html)를 사용하여 요소를 제거하는 보다 간결한 방법을 제공하는 새로운 메서드를 Collection 인터페이스에 도입 했다.

```java
names.removeIf(e -> e.startsWith("A"));
```

반복자 접근 방식과 달리 [removeIf](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collection.html#removeIf(java.util.function.Predicate))는 LinkedList와 ArrayList 모두에서 비슷하게 잘 수행된다.

Java 8에서 ArrayList는 Iterator에 의존하는 기본 구현을 재정의 하고 다른 전략을 구현한다. 먼저 요소를 반복하고 Predicate와 일치하는 항목을 표시한다. 그런 다음 첫 번째 반복에서 표시된 요소를 제거(및 이동)하기 위해 두 번째 반복한다.

## 4. 자바 8과 스트림의 도입
Java 8의 새로운 주요 기능 중 하나는 Stream(및 Collector)의 추가다. 소스에서 Stream을 만드는 방법에는 여러 가지가 있다. 그러나 Stream 인스턴스에 영향을 미치는 대부분의 작업은 소스를 변경하지 않고 API는 소스의 복사본을 만들고 필요한 작업을 수행하는 데 중점을 둔다.

Stream 및 Collector를 사용하여 Predicate와 일치하는 요소와 일치하지 않는 요소를 찾고/필터링하는 방법이다.

### 1) 스트림으로 요소 제거
Stream을 사용하여 요소를 필터링 하는 것은 매우 간단하다. Collection을 사용하여 Stream의 인스턴스를 생성하고 Predicate로 필터를 호출한 다음 Collector의 도움으로 결과를 [collect](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Stream.html#collect(java.util.stream.Collector))하기 만 하면 된다.

```java
Collection<String> filteredCollection = names
  .stream()
  .filter(e -> !e.startsWith("A"))
  .collect(Collectors.toList());
```

스트리밍은 이전 접근 방식보다 덜 침습적이며 격리를 촉진하고 동일한 소스에서 여러 복사본을 생성할 수 있다. 그러나 애플리케이션에서 사용하는 메모리도 증가한다는 점을 염두에 두어야 한다.

### 2) Collectors.partitioningBy
`Stream.filter`와 수집기를 결합하는 것은 매우 편리하지만 일치하는 요소와 일치하지 않는 요소가 모두 필요한 시나리오에 직면할 수 있다. 이러한 경우 [Collectors.partitioningBy](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/Collectors.html#partitioningBy(java.util.function.Predicate,java.util.stream.Collector))를 활용할 수 있다.

```java
Map<Boolean, List<String>> classifiedElements = names
    .stream()
    .collect(Collectors.partitioningBy((String e) -> 
      !e.startsWith("A")));

String matching = String.join(",", classifiedElements.get(true));
String nonMatching = String.join(",", classifiedElements.get(false));
```

이 메서드는 각각 일치하는 요소와 일치하지 않는 요소를 포함하는 목록을 가리키는 두 개의 키(true 및 false)만 포함하는 Map을 반환한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-collection-remove-elements](https://www.baeldung.com/java-collection-remove-elements)
