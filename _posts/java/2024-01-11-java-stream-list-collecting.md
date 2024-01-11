---
title: Java 스트림 Elements를 리스트로 수집
author: dejavuhyo
date: 2024-01-11 11:20:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-list, list-collecting, 자바-스트림, 스트림-리스트, 리스트-수집]
---

## 1. 스트림 요소를 리스트로 수집
스트림에서 리스트를 가져오는 것은 스트림 파이프라인에서 가장 많이 사용되는 터미널 작업 이다. Java 16 이전에는 `Stream.collect()` 메서드를 호출하고 이를 수집기에 인수로 전달하여 요소를 수집했다. Collector 자체는 `Collectors.toList()` 메서드를 호출하여 생성되었다.

그러나 Stream 인스턴스에서 직접 List를 가져오는 메서드에 대한 변경 요청이 있었다. Java 16 릴리스에서는 이제 Stream에서 직접 새로운 메서드인 `toList()`를 호출하여 List를 가져올 수 있다. StreamEx와 같은 라이브러리는 Stream에서 직접 리스트를 가져오는 편리한 방법도 제공한다.

다음을 사용하여 Stream 요소를 List에 누적할 수 있다.

* `Stream.collect(Collectors.toList())`: Java 8부터

* `Stream.collect(Collectors.toUnmodifiableList())`: Java 10부터

* `Stream.toList()`: Java 16부터

릴리스의 시간순으로 이러한 방법을 사용한다.

## 2. 리스트 분석
이전에 설명한 방법으로 리스트를 만든다. 그런 다음 속성을 분석한다.

모든 예에 대해 다음과 같은 국가 코드 스트림을 사용한다.

```java
Stream.of(Locale.getISOCountries());
```

### 1) 리스트 만들기
다양한 방법을 사용하여 지정된 국가 코드 스트림에서 리스트를 생성한다.

먼저 `Collectors:toList()`를 사용하여 리스트를 만든다.

```java
List<String> result = Stream.of(Locale.getISOCountries()).collect(Collectors.toList());
```

그런 다음 `Collectors.toUnmodifyingList()`를 사용하여 이를 수집한다.

```java
List<String> result = Stream.of(Locale.getISOCountries()).collect(Collectors.toUnmodifiableList());
```

여기에서는 이러한 메소드에서 Collector 인터페이스를 통해 스트림을 리스트에 축적한다. Stream을 직접 사용하지 않기 때문에 추가 할당 및 복사가 발생한다.

다음으로 `Stream.toList()`를 사용하여 수집을 반복한다 .

```java
List<String> result = Stream.of(Locale.getISOCountries()).toList();
```

여기서는 스트림에서 직접 목록을 가져오므로 추가 할당 및 복사가 방지된다.

결과적으로 **Stream에서 직접 `toList()`를 사용하는 것은 다른 두 호출과 비교할 때 더 간결하고 깔끔하고 편리하며 최적이다.**

### 2) 누적된 리스트 검사
먼저 리스트 유형을 살펴본다.

`Collectors.toList()`는 Stream 요소를 ArrayList로 수집한다.

```java
java.util.ArrayList
```

`Collectors.toUnmodifyingList()`는 Stream 요소를 수정 불가능한 리스트로 수집한다.

```java
java.util.ImmutableCollections.ListN
```

`Stream.toList()`는 수정 불가능한 리스트로 요소를 수집한다.

```java
java.util.ImmutableCollections.ListN
```

`Collectors.toList()`의 현재 구현은 변경 가능한 리스트를 생성 하지만 메서드 사양 자체는 리스트의 유형, 변경 가능성, 직렬화 가능성 또는 스레드 안전성을 보장하지 않는다.

반면 `Collectors.toUnmodifyingList()` 및 `Stream.toList()`는 모두 수정 불가능한 목록을 생성한다.

이는 `Collectors.toList()` 요소에 대해 추가 및 정렬과 같은 작업을 수행할 수 있지만 `Collectors.toUnmodifyingList()` 및 `Stream.toList()` 요소에서는 수행할 수 없음을 의미한다. 

### 3) 리스트에 Null 요소 허용
`Stream.toList()`는 수정 불가능한 리스트를 생성 하지만 여전히 `Collectors.toUnmodifyingList()`와 동일하지 않다. 이는 `Stream.toList()`가 null 요소를 허용 하고 `Collectors.toUnmodifyingList()`가 null 요소를 허용하지 않기 때문이다. 그러나 `Collectors.toList()`는 null 요소를 허용한다.

`Collectors.toList()`는 null 요소가 포함된 스트림이 수집될 때 예외를 발생시키지 않는다.

```java
Assertions.assertDoesNotThrow(() -> {
    Stream.of(null,null).collect(Collectors.toList());
});
```

`Collectors.toUnmodifyingList()`는 null 요소가 포함된 스트림을 수집할 때 NulPointerException을 발생시킨다.

```java
Assertions.assertThrows(NullPointerException.class, () -> {
    Stream.of(null,null).collect(Collectors.toUnmodifiableList());
});
```

`Stream.toList()`는 null 요소가 포함된 Stream을 수집하려고 할 때 NulPointerException을 발생시키지 않는다.

```java
Assertions.assertDoesNotThrow(() -> {
    Stream.of(null,null).toList();
});
```

**따라서 이는 코드를 Java 8에서 Java 10 또는 Java 16으로 마이그레이션할 때 주의해야 할 사항이다. `Collectors.toList()` 또는 `Collectors.toUnmodifyingList()` 대신 `Stream.toList()`를 맹목적으로 사용할 수 없다.**

### 4) 분석 요약
리스트의 차이점과 유사점이 요약되어 있다.

![stream-list-summary](/assets/img/2024-01-11-java-stream-list-collecting/stream-list-summary.png)

## 3. 다른 toList() 메소드를 사용해야 하는 경우
`Stream.toList()`를 추가하는 주요 목적은 Collector API의 자세한 내용을 줄이는 것이다.

List를 가져오기 위해 수집기 메서드를 사용하는 것은 매우 장황하다. 반면 `Stream.toList()` 메서드를 사용하면 코드가 깔끔하고 간결해진다.

그럼에도 불구하고 `Stream.toList()`는 `Collectors.toList()` 또는 `Collectors.toUnmodifyingList()`에 대한 바로 가기로 사용할 수 없다.

`Stream.toList()`는 구현이 Collector 인터페이스와 독립적이기 때문에 메모리를 덜 사용한다. Stream 요소를 List에 직접 축적한다. 따라서 스트림의 크기를 미리 알고 있다면 `Stream.toList()`를 사용하는 것이 가장 좋다.

또한 Stream API는 `toList()` 메서드에 대해서만 구현을 제공한다는 것도 알고 있다. 맵이나 set을 가져오는 유사한 방법이 포함되어 있지 않다. 따라서 리스트, 맵 또는 set과 같은 변환기를 얻기 위해 통일된 접근 방식을 원한다면 계속해서 Collector API를 사용할 것이다. 이는 또한 일관성을 유지하고 혼란을 방지한다.

마지막으로 Java 16보다 낮은 버전을 사용하는 경우 Collectors 메서드를 계속 사용해야 한다.

다음 표에는 주어진 방법의 최적 사용이 요약되어 있다.

![comparison](/assets/img/2024-01-11-java-stream-list-collecting/comparison.png)

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-to-list-collecting](https://www.baeldung.com/java-stream-to-list-collecting)
