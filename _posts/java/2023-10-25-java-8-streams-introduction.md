---
title: Java 8 Streams Introduction
author: dejavuhyo
date: 2023-10-25 22:15:00 +0900
categories: [Language, Java]
tags: [java-streams, streams, 자바-스트림, 스트림]
---

## 1. 스트림 API
Java 8의 새로운 주요 기능 중 하나는 요소 시퀀스를 처리하기 위한 클래스가 포함된 스트림 기능(`java.util.stream`)의 도입이다.

핵심 API 클래스는 `Stream<T>` 이다. 예제에서는 기존 데이터 공급자 소스를 사용하여 스트림을 생성하는 방법을 보여준다.

### 1) 스트림 생성
스트림은 `stream()` 및 `of()` 메소드를 사용하여 컬렉션 또는 배열과 같은 다양한 요소 소스에서 생성될 수 있다.

```java
String[] arr = new String[]{"a", "b", "c"};
Stream<String> stream = Arrays.stream(arr);
stream = Stream.of("a", "b", "c");
```

`stream()` 기본 메소드가 Collection 인터페이스에 추가되어 임의의 컬렉션을 요소 소스로 사용하여 `Stream<T>`을 생성할 수 있다.

```java
Stream<String> stream = list.stream();
```

### 2)스트림을 사용한 멀티스레딩
또한 Stream API는 병렬 모드에서 스트림 요소에 대한 작업을 실행하는 `parallelStream()` 메서드를 제공하여 멀티스레딩을 단순화한다.

아래 코드를 사용하면 스트림의 모든 요소에 대해 `doWork()` 메서드를 병렬로 실행할 수 있다.

```java
list.parallelStream().forEach(element -> doWork(element));
```

## 2. 스트림 운영
스트림에서 수행할 수 있는 유용한 작업이 많이 있다.

이는 중간 작업 (`Stream<T>` 반환)과 터미널 작업 (확정된 유형의 결과 반환)으로 구분된다. 중간 작업은 연결을 허용한다.

스트림에 대한 작업이 소스를 변경하지 않는다는 점도 주목할 가치가 있다.

간단한 예는 다음과 같다.

```java
long count = list.stream().distinct().count();
```

따라서 `distinct()` 메소드는 이전 스트림의 고유 요소로 구성된 새로운 스트림을 생성하는 중간 작업을 나타낸다. 그리고 `count()` 메소드는 스트림의 크기를 반환하는 터미널 작업이다.

### 1) Iterating
Stream API는 for, for-each 및 while 루프를 대체하는데 도움이 된다. 이를 통해 작업 논리에 집중할 수 있지만 요소 시퀀스에 대한 반복에는 집중할 수 없다. 예제이다.

```java
for (String string : list) {
    if (string.contains("a")) {
        return true;
    }
}
```

이 코드는 Java 8 코드 한 줄로 변경할 수 있다.

```java
boolean isExist = list.stream().anyMatch(element -> element.contains("a"));
```

### 2) Filtering
`filter()` 메소드를 사용하면 조건자를 만족하는 요소의 스트림을 선택할 수 있다.

예를 들어 다음 목록을 고려한다.

```java
ArrayList<String> list = new ArrayList<>();
list.add("One");
list.add("OneAndOnly");
list.add("Derek");
list.add("Change");
list.add("factory");
list.add("justBefore");
list.add("Italy");
list.add("Italy");
list.add("Thursday");
list.add("");
list.add("");
```

다음 코드는 `List<String>`의 `Stream<String>`을 생성하고, char "d" 를 포함하는 이 스트림의 모든 요소를 ​​찾고, 필터링된 요소만 포함하는 새 스트림을 생성한다.

```java
Stream<String> stream = list.stream().filter(element -> element.contains("d"));
```

### 3) Mapping
특수 함수를 적용하여 Stream의 요소를 변환 하고 이러한 새 요소를 Stream으로 수집하려면 `map()` 메서드를 사용할 수 있다.

```java
List<String> uris = new ArrayList<>();
uris.add("C:\\My.txt");
Stream<Path> stream = uris.stream().map(uri -> Paths.get(uri));
```

따라서 위의 코드는 초기 Stream의 모든 요소에 특정 람다 식을 적용하여 `Stream<String>`을 `Stream<Path>`로 변환한다.

모든 요소가 고유한 요소 시퀀스를 포함하는 스트림이 있고 이러한 내부 요소의 스트림을 생성하려는 경우 `flatMap()` 메서드를 사용해야 한다.

```java
List<Detail> details = new ArrayList<>();
details.add(new Detail());
Stream<String> stream = details.stream().flatMap(detail -> detail.getParts().stream());
```

이 예에는 Detail 유형의 요소 목록이 있다. Detail 클래스에는 `List<String>`인 PARTS 필드가 포함되어 있다. `flatMap()` 메소드의 도움으로 PARTS 필드의 모든 요소가 추출되어 새로운 결과 스트림에 추가된다. 그 후에는 초기 `Stream<Detail>`이 손실된다.

### 4) Matching
Stream API는 일부 조건에 따라 시퀀스 요소의 유효성을 검사할 수 있는 편리한 도구 세트를 제공한다. 이를 위해 `anyMatch()`, `allMatch()`, `noneMatch()` 메서드 중 하나를 사용할 수 있다. 그들의 이름은 자명한다. boolean을 반환하는 터미널 작업은 다음과 같다.

```java
boolean isValid = list.stream().anyMatch(element -> element.contains("h")); // true
boolean isValidOne = list.stream().allMatch(element -> element.contains("h")); // false
boolean isValidTwo = list.stream().noneMatch(element -> element.contains("h")); // false
```

빈 스트림의 경우, 주어진 조건자가 있는 `allMatch()` 메서드는 true를 반환한다.

```java
Stream.empty().allMatch(Objects::nonNull); // true
```

조건자를 만족하지 않는 요소를 찾을 수 없으므로 이는 합리적인 기본값이다.

마찬가지로, `anyMatch()` 메서드는 빈 스트림에 대해 항상 false를 반환한다.

```java
Stream.empty().anyMatch(Objects::nonNull); // false
```

다시 말하지만, 이 조건을 만족하는 요소를 찾을 수 없기 때문에 이는 합리적이다.

### 5) Reduction
Stream API를 사용하면 Stream 유형의 `Reduce()` 메서드를 사용하여 지정된 함수에 따라 일련의 요소를 일부 값으로 줄일 수 있다. 이 방법은 두 가지 매개변수를 사용한다. 첫 번째 – 시작 값, 두 번째 – 누산기 함수이다.

`List<Integer>`가 있고 이러한 모든 요소와 일부 초기 Integer (이 예에서는 23)의 합계를 갖고 싶다고 생각한다. 따라서 다음 코드를 실행하면 결과는 26(23 + 1 + 1 + 1)이 된다.

```java
List<Integer> integers = Arrays.asList(1, 1, 1);
Integer reduced = integers.stream().reduce(23, (a, b) -> a + b);
```

### 6) Collecting
Stream 유형의 `Collect()` 메소드를 통해 축소를 제공할 수도 있다. 이 작업은 스트림을 Collection 이나 Map으로 변환 하고 스트림을 단일 문자열 형식으로 표현하는 경우 매우 편리하다. 거의 모든 일반적인 수집 작업에 대한 솔루션을 제공하는 유틸리티 클래스 Collectors가 있다. 사소하지 않은 일부 작업의 경우 사용자 지정 수집기를 만들 수 있다.

```java
List<String> resultList = list.stream().map(element -> element.toUpperCase()).collect(Collectors.toList());
```

이 코드는 터미널 `Collect()` 작업을 사용하여 `Stream<String>`을 `List<String>`으로 줄인다.

## [출처 및 참고]
* [https://www.baeldung.com/java-8-streams-introduction](https://www.baeldung.com/java-8-streams-introduction)
