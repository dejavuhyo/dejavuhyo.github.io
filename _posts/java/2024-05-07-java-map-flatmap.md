---
title: Java map()과 flatMap()의 차이점
author: dejavuhyo
date: 2024-05-07 12:36:00 +0900
categories: [Language, Java]
tags: [java-map, java-flatmap, map-flatmap, map-flatmap-차이점]
---

## 1. Optional의 Map과 Flatmap
`map()` 메소드는 Optional과 잘 작동한다. 함수가 필요한 정확한 유형을 반환하는 경우이다.

```java
Optional<String> s = Optional.of("test");
assertEquals(Optional.of("TEST"), s.map(String::toUpperCase));
```

그러나 더 복잡한 경우에는 Optional을 반환하는 함수도 제공될 수 있다. 이러한 경우 `map()`을 사용하면 `map()` 구현이 내부적으로 추가 래핑을 수행하므로 중첩된 구조가 발생한다.

이 상황을 더 잘 이해하기 위한 또 다른 예이다.

```java
assertEquals(Optional.of(Optional.of("STRING")), 
  Optional
  .of("string")
  .map(s -> Optional.of("STRING")));
```

보시다시피, 중첩 구조 `Optional<Optional<String>>`으로 끝난다. 작동하기는 하지만 사용하기가 꽤 번거롭고 추가적인 null 안전성을 제공하지 않으므로 플랫 구조를 유지하는 것이 좋다.

이것이 바로 `flatMap()`이 수행하는데 도움이 된다.

```java
assertEquals(Optional.of("STRING"), Optional
  .of("string")
  .flatMap(s -> Optional.of("STRING")));
```

## 2. Streams의 Map과 Flatmap
두 방법 모두 Optional에 대해 유사하게 작동한다.

`map()` 메서드는 Stream 인스턴스의 기본 시퀀스를 래핑하는 반면 `flatMap()` 메서드를 사용하면 중첩된 `Stream<Stream<R>>` 구조를 피할 수 있다.

여기서 `map()`은 입력 Stream의 요소에 `toUpperCase()` 메서드를 적용한 결과로 구성된 Stream을 생성한다.

```java
List<String> myList = Stream.of("a", "b")
  .map(String::toUpperCase)
  .collect(Collectors.toList());
assertEquals(asList("A", "B"), myList);
```

`map()`은 이러한 간단한 경우에 잘 작동한다. 하지만 입력으로 list의 lists 같이 더 복잡한 것이 있으면 어떻게 작동하는지 확인한다.

```java
List<List<String>> list = Arrays.asList(
  Arrays.asList("a"),
  Arrays.asList("b"));
System.out.println(list);
```

목록 `[[a], [b]]` 목록을 인쇄한다.

이제 `flatMap()`을 사용한다.

```java
System.out.println(list
  .stream()
  .flatMap(Collection::stream)
  .collect(Collectors.toList()));
```

이러한 조각의 결과는 `[a, b]`로 평면화된다.

`flatMap()` 메서드는 먼저 입력 Stream의 Streams을 문자열 스트림으로 [평면화](https://www.baeldung.com/java-flatten-nested-collections)한다. 이후에는 `map()` 메서드와 유사하게 작동한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-difference-map-and-flatmap](https://www.baeldung.com/java-difference-map-and-flatmap)
