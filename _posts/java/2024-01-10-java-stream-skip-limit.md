---
title: Java 스트림 skip()과 limit()
author: dejavuhyo
date: 2024-01-10 10:30:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-skip, stream-limit, 자바-스트림, 스트림-skip, 스트림-limit]
---

## 1. Skip() 메소드
`Skip(n)` 메서드는 스트림의 처음 n개 요소를 삭제하는 중간 작업 이다. n 매개 변수는 음수일 수 없으며 스트림 크기보다 큰 경우 `Skip()`은 빈 스트림을 반환한다.

예이다.

```java
Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    .filter(i -> i % 2 == 0)
    .skip(2)
    .forEach(i -> System.out.print(i + " "));
```

이 스트림에서는 스트림의 짝수를 선택하지만 처음 두 개는 건너뛴다. 따라서 결과는 다음과 같다.

```text
6 8 10
```

이 스트림이 실행되면 forEach가 항목을 요청하기 시작합니다. `Skip()`에 도달하면 이 작업은 처음 두 항목을 버려야 한다는 것을 알고 있으므로 결과 스트림에 추가하지 않는다. 그런 다음 나머지 항목이 포함된 스트림을 생성하고 반환한다.

이를 위해서는 `Skip()` 작업이 매 순간 표시되는 요소의 상태를 유지해야 한다. 이러한 이유로 **`Skip()`을 상태 저장 작업이라고 말한다.**

## 2. Limit() 메소드
`Limit(n)` 메소드는 요청된 크기보다 길지 않은 스트림을 반환하는 또 다른 중간 작업이다. 이전과 마찬가지로 n 매개변수는 음수일 수 없다.

예이다.

```java
Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    .filter(i -> i % 2 == 0)
    .limit(2)
    .forEach(i -> System.out.print(i + " "));
```

이 경우 int 스트림에서 단 두 개의 짝수만 선택한다.

```text
2 4
```

`Skip()` 작업의 경우와 마찬가지로, `Limit()`도 선택되는 항목의 상태를 유지해야 하기 때문에 상태 저장 작업이다.

그러나 전체 스트림을 소비하는 `Skip()`과 달리, `Limit()`은 최대 항목 수에 도달하자마자 더 이상 항목을 소비하지 않고 결과 스트림을 반환한다. 따라서 `Limit()`이 단락 연산이라고 말한다.

무한 스트림으로 작업할 때, `Limit()`는 스트림을 유한 스트림으로 자르는데 매우 유용할 수 있다.

```java
Stream.iterate(0, i -> i + 1)
    .filter(i -> i % 2 == 0)
    .limit(10)
    .forEach(System.out::println);
```

이 예에서는 무한한 숫자 스트림을 10개의 짝수만 포함하는 스트림으로 자른다.

## 3. Skip()과 Limit() 결합
앞서 언급했듯이 skip과 limit 작업은 상호 보완적이며 이를 결합하면 경우에 따라 매우 도움이 될 수 있다.

이전 예제를 수정하여 10개의 일괄 처리에서 짝수를 가져오고 싶다고 가정한다. 동일한 스트림에서 `Skip()` 과 `Limit()`을 모두 사용하면 간단히 그렇게 할 수 있다.

```java
private static List<Integer> getEvenNumbers(int offset, int limit) {
    return Stream.iterate(0, i -> i + 1)
        .filter(i -> i % 2 == 0)
        .skip(offset)
        .limit(limit)
        .collect(Collectors.toList());
}
```

이 방법을 사용하면 스트림을 통해 아주 쉽게 페이지를 매길 수 있다. 이는 매우 단순한 페이지 매김이지만 스트림을 분할할 때 이것이 얼마나 강력한지 알 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-skip-vs-limit](https://www.baeldung.com/java-stream-skip-vs-limit)
