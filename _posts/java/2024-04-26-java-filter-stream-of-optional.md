---
title: Java Stream Optionals 필터링
author: dejavuhyo
date: 2024-04-26 19:51:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-optionals, stream-optionals-filtering, optionals-filtering]
---

## 1. Stream of Optionals
Stream of Optionals에서 비어 있지 않은 값을 필터링하는 방법이다.

모든 예에서 동일한 list를 사용한다.

```java
List<Optional<String>> listOfOptionals = Arrays.asList(
  Optional.empty(), Optional.of("foo"), Optional.empty(), Optional.of("bar"));
```

## 2. filter() 사용
Java 8의 옵션 중 하나는 `Optional::isPresent`를 사용하여 값을 필터링한 다음 `Optional::get` 함수를 사용하여 매핑을 수행하여 값을 추출하는 것이다.

```java
List<String> filteredList = listOfOptionals.stream()
  .filter(Optional::isPresent)
  .map(Optional::get)
  .collect(Collectors.toList());
```

## 3. flatMap() 사용
다른 옵션은 빈 Optional을 빈 Stream 인스턴스로 변환하고, 비어 있지 않은 Optional을 하나의 요소만 포함하는 Stream 인스턴스로 변환하는 람다 식과 함께 flatMap을 사용하는 것이다.

```java
List<String> filteredList = listOfOptionals.stream()
  .flatMap(o -> o.isPresent() ? Stream.of(o.get()) : Stream.empty())
  .collect(Collectors.toList());
```

또는 Optional을 Stream으로 변환하는 다른 방법을 사용하여 동일한 접근 방식을 적용할 수 있다.

```java
List<String> filteredList = listOfOptionals.stream()
  .flatMap(o -> o.map(Stream::of).orElseGet(Stream::empty))
  .collect(Collectors.toList());
```

## 4. Java 9의 Optional::stream
Optional에 `stream()` 메소드를 추가하는 Java 9의 등장으로 이 모든 것이 상당히 단순화될 것이다.

이 접근 방식은 위에 표시된 것과 유사하지만 이번에는 Optional 인스턴스를 Stream 인스턴스로 변환하기 위해 미리 정의된 방법을 사용한다.

Optional 값이 있든 없든 요소가 1개 또는 0개인 스트림을 반환한다.

```java
List<String> filteredList = listOfOptionals.stream()
  .flatMap(Optional::stream)
  .collect(Collectors.toList());
```

## [출처 및 참고]
* [https://www.baeldung.com/java-filter-stream-of-optional](https://www.baeldung.com/java-filter-stream-of-optional)
