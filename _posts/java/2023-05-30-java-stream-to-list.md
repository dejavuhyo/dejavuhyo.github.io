---
title: Java Stream Elements를 List로 수집
author: dejavuhyo
date: 2023-05-30 09:05:00 +0900
categories: [Language, Java]
tags: [java-stream-list, stream-list, java-stream, java-list, 자바-스트림, 자바-리스트, 스트림-리스트]
---

## 1. 스트림 요소를 List로 수집
스트림에서 List 가져오기는 스트림 파이프라인에서 가장 많이 사용되는 터미널 작업이다. Java 16 이전에는 `Stream.collect()` 메서드를 호출 하고 요소를 수집하기 위한 인수로 Collector에 전달했다. Collector 자체는 `Collectors.toList()` 메서드를 호출하여 생성되었다.

그러나 스트림 인스턴스에서 직접 List를 가져오는 방법에 대한 변경 요청이 있었다. Java 16 릴리스에서는 이제 Stream에서 직접 새로운 메서드인 `toList()`를 호출하여 List를 가져올 수 있다. StreamEx와 같은 라이브러리는 Stream에서 직접 List를 가져오는 편리한 방법도 제공한다.

다음을 사용하여 Stream 요소를 List로 축적할 수 있다.

* `Stream.collect(Collectors.toList())`: Java 8부터

* `Stream.collect( Collectors.toUnmodifiableList() )`: Java 10부터

* `Stream.toList()`: Java 16부터

## 2. List 분석
먼저 List를 만든다. 그런 다음 속성을 분석한다.

모든 예에 대해 다음과 같은 국가 코드 스트림을 사용한다.

```java
Stream.of(Locale.getISOCountries());
```

### 1) List 만들기
다른 방법을 사용하여 주어진 국가 코드 스트림에서 List를 만든다.

먼저 `Collectors:toList()`를 사용하여 List를 만든다.

```java
List<String> result = Stream.of(Locale.getISOCountries()).collect(Collectors.toList());
```

그런 다음 `Collectors.toUnmodifiableList()`를 사용하여 수집한다.

```java
List<String> result = Stream.of(Locale.getISOCountries()).collect(Collectors.toUnmodifiableList());
```

이러한 방법에서는 수집기 인터페이스를 통해 스트림을 List에 축적한다. 스트림과 직접 작업하지 않기 때문에 추가 할당 및 복사가 발생한다.

다음으로 `Stream.toList()`를 사용하여 수집을 반복한다.

```java
List<String> result = Stream.of(Locale.getISOCountries()).toList();
```

스트림에서 직접 List를 가져오므로 추가 할당 및 복사를 방지한다.

결과적으로 스트림에서 직접 `toList()`를 사용하는 것이 다른 두 호출과 비교할 때 더 간결하고 깔끔하고 편리하며 최적이다.

### 2) 누적 List 검토
List의 유형을 검토한다.

`Collectors.toList()`는 Stream 요소를 ArrayList로 수집한다.

```java
java.util.ArrayList
```

`Collectors.toUnmodifiableList()`는 Stream 요소를 수정 불가능한 List로 수집한다.

```java
java.util.ImmutableCollections.ListN
```

`Stream.toList()`는 요소를 수정할 수 없는 List로 수집한다.

```java
java.util.ImmutableCollections.ListN
```

`Collectors.toList()`의 현재 구현은 변경 가능한 List를 생성 하지만 메서드의 사양 자체는 List의 유형, 변경 가능성, 직렬화 가능성 또는 스레드 안전성을 보장하지 않는다.

반면에 `Collectors.toUnmodifiableList()` 및 `Stream.toList()`는 수정 불가능한 List를 생성한다.

이는 `Collectors.toList()` 요소에 대해 추가 및 정렬과 같은 작업을 수행할 수 있지만 `Collectors.toUnmodifiableList()` 및 `Stream.toList()` 요소에 대해서는 수행할 수 없음을 의미한다.

### 3) List에서 Null 요소 허용
`Stream.toList()`는 수정할 수 없는 List를 생성 하지만 여전히 `Collectors.toUnmodifiableList()`와 동일하지는 않다. 이는 `Stream.toList()`가 null 요소를 허용 하고 `Collectors.toUnmodifiableList()`가 null 요소를 허용하지 않기 때문이다. 그러나 `Collectors.toList()`는 null 요소를 허용한다.

null 요소를 포함하는 스트림이 수집될 때 `Collectors.toList()`는 예외를 발생하지 않는다.

```java
Assertions.assertDoesNotThrow(() -> {
    Stream.of(null,null).collect(Collectors.toList());
});
```

null 요소를 포함하는 스트림을 수집할 때 `Collectors.toUnmodifiableList()`는 NulPointerException을 발생시킨다.

```java
Assertions.assertThrows(NullPointerException.class, () -> {
    Stream.of(null,null).collect(Collectors.toUnmodifiableList());
});
```

`Stream.toList()`는 null 요소를 포함하는 Stream을 수집하려고 할 때 NulPointerException을 발생시키지 않는다.

```java
Assertions.assertDoesNotThrow(() -> {
    Stream.of(null,null).toList();
});
```

**따라서 Java 8에서 Java 10 또는 Java 16으로 코드를 마이그레이션할 때 주의해야 한다. `Collectors.toList()` 또는 `Collectors.toUnmodifiableList()` 대신 `Stream.toList()`를 맹목적으로 사용할 수는 없다.**

### 4) 분석 요약
다음 표에는 분석에서 List의 차이점과 유사점이 요약되어 있다.

![stream-list-summary](/assets/img/2023-05-30-java-stream-to-list/stream-list-summary.png)

## 3. 다른 toList() 메서드를 사용해야 하는 경우
`Stream.toList()`를 추가하는 주요 목적은 Collector API의 장황함을 줄이는 것이다.

List를 얻기 위해 Collectors 메서드를 사용하는 것은 매우 장황하다. 반면에 `Stream.toList()` 메서드를 사용하면 코드가 깔끔하고 간결해진다.

그럼에도 불구하고 `Stream.toList()`는 `Collectors.toList()` 또는 `Collectors.toUnmodifiableList()`에 대한 바로 가기로 사용할 수 없다.

`Stream.toList()`는 구현이 Collector 인터페이스와 독립적이기 때문에 메모리를 덜 사용한다. Stream 요소를 List에 직접 축적한다. 따라서 스트림의 크기를 미리 알고 있으면 `Stream.toList()`를 사용하는 것이 가장 좋다.

또한 Stream API가 `toList()` 메서드에 대해서만 구현을 제공한다. 맵이나 집합을 가져오는 유사한 메서드가 포함되어 있지 않다. 따라서 list, map 또는 set과 같은 변환기를 가져오기 위한 균일한 접근 방식을 원하는 경우 Collector API를 계속 사용한다. 이것은 또한 일관성을 유지하고 혼란을 피할 것이다.

Java 16보다 낮은 버전을 사용하는 경우 수집기 메서드를 계속 사용해야 한다.

다음 표에는 주어진 방법의 최적 사용이 요약되어 있다.

![comparison](/assets/img/2023-05-30-java-stream-to-list/comparison.png)

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-to-list-collecting](https://www.baeldung.com/java-stream-to-list-collecting)
