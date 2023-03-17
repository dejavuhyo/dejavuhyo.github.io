---
title: Java Arraylist 중복 Elements 계산
author: dejavuhyo
date: 2023-03-17 11:25:00 +0900
categories: [Language, Java]
tags: [java-list, java-duplicate-elements, duplicate-elements-arraylist, arraylist-duplicate, 자바-리스트, 어레이리스트-엘리먼드, 어레이리스트-중복-엘리먼드]
---

## 1. Map.put()을 사용한 루프
예상 결과는 입력 목록의 모든 요소를 키로 포함하고 각 요소의 개수를 값으로 포함하는 Map 개체이다.

이를 달성하기 위한 가장 간단한 솔루션은 입력 목록을 통해 각 요소에 대해 반복하는 것이다.

* resultMap에 요소가 포함되어 있으면 카운터를 1씩 증가시킨다.

* 그렇지 않으면 새 맵 항목 (element, 1)을 맵에 넣는다.

```java
public <T> Map<T, Long> countByClassicalLoop(List<T> inputList) {
    Map<T, Long> resultMap = new HashMap<>();
    for (T element : inputList) {
        if (resultMap.containsKey(element)) {
            resultMap.put(element, resultMap.get(element) + 1L);
        } else {
            resultMap.put(element, 1L);
        }
    }
    return resultMap;
}
```

이 구현은 모든 최신 Java 버전에서 작동하므로 최상의 호환성을 갖는다.

Java 8 이전 호환성이 필요하지 않은 경우 방법을 더 단순화할 수 있다.

```java
public <T> Map<T, Long> countByForEachLoopWithGetOrDefault(List<T> inputList) {
    Map<T, Long> resultMap = new HashMap<>();
    inputList.forEach(e -> resultMap.put(e, resultMap.getOrDefault(e, 0L) + 1L));
    return resultMap;
}
```

메서드를 테스트하기 위한 입력 목록이다.

```java
private List<String> INPUT_LIST = Lists.list(
  "expect1",
  "expect2", "expect2",
  "expect3", "expect3", "expect3",
  "expect4", "expect4", "expect4", "expect4");
```

확인한다.

```java
private void verifyResult(Map<String, Long> resultMap) {
    assertThat(resultMap)
      .isNotEmpty().hasSize(4)
      .containsExactly(
        entry("expect1", 1L),
        entry("expect2", 2L),
        entry("expect3", 3L),
        entry("expect4", 4L));
}
```

나머지 접근 방식에 대해 이 테스트 도구를 재사용할 것이다.

## 2. Map.compute()를 사용한 루프
Java 8에서는 간편한 `compute()` 메서드가 Map 인터페이스에 도입되었다. 이 방법도 사용할 수 있다.

```java
public <T> Map<T, Long> countByForEachLoopWithMapCompute(List<T> inputList) {
    Map<T, Long> resultMap = new HashMap<>();
    inputList.forEach(e -> resultMap.compute(e, (k, v) -> v == null ? 1L : v + 1L));
    return resultMap;
}
```

`(k, v) -> v == null ? 1L : v + 1L` 은 `BiFunction<T, Long, Long>` 인터페이스를 구현한 리매핑 함수이다. 주어진 키에 대해 1씩 증가된 현재 값을 반환하거나(키가 이미 맵에 있는 경우) 기본값 1을 반환한다.

코드의 가독성을 높이기 위해 리매핑 함수를 해당 변수로 추출하거나 `countByForEachLoopWithMapCompute`의 입력 매개변수로 사용할 수도 있다.

## 3. Map.merge()를 사용한 루프
`Map.compute()`를 사용할 때 null 값을 명시적으로 처리해야 한다. 예를 들어 지정된 키에 대한 매핑이 존재하지 않는 경우이다. 이것이 리매핑 기능에서 null 검사를 구현한 이유이다. 그러나 이것은 예쁘게 보이지 않는다.

`Map.merge()` 메서드를 사용하여 코드를 정리한다.

```java
public <T> Map<T, Long> countByForEachLoopWithMapMerge(List<T> inputList) {
    Map<T, Long> resultMap = new HashMap<>();
    inputList.forEach(e -> resultMap.merge(e, 1L, Long::sum));
    return resultMap;
}
```

`merge()`는 지정된 키에 대한 매핑이 존재하지 않거나 해당 값이 null인 경우 키를 제공된 값과 연결한다. 그렇지 않으면 리매핑 기능을 사용하여 새 값을 계산하고 그에 따라 매핑을 업데이트한다.

이번에는 `Long::sum`을 `BiFunction<T, Long, Long>` 인터페이스 구현으로 사용했다.

## 4. Stream API Collectors.toMap()
Java 8의 Stream API 덕분에 매우 간결한 방식으로 문제를 해결할 수 있다.

`toMap()` 수집기는 입력 목록을 Map으로 변환하는 데 도움이 된다.

```java
public <T> Map<T, Long> countByStreamToMap(List<T> inputList) {
    return inputList.stream().collect(Collectors.toMap(Function.identity(), v -> 1L, Long::sum));
}
```

`toMap()`은 스트림을 다른 Map 구현으로 변환하는 데 도움이 되는 편리한 수집기 이다.

## 5. Stream API Collectors.groupingBy()과 Collectors.counting()
`toMap()`을 제외하고 `groupingBy()` 및 `counting()`이라는 두 개의 다른 수집기로 해결할 수 있다.

```java
public <T> Map<T, Long> countByStreamGroupBy(List<T> inputList) {
    return inputList.stream().collect(Collectors.groupingBy(k -> k, Collectors.counting()));
}
```

Java 8 수집기를 적절하게 사용하면 코드가 간결해지고 읽기 쉬워진다.

## [출처 및 참고]
* [https://www.baeldung.com/java-count-duplicate-elements-arraylist](https://www.baeldung.com/java-count-duplicate-elements-arraylist)
