---
title: Java Stream peek()
author: dejavuhyo
date: 2024-06-04 13:59:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-peek, 자바-스트림, 스트림-peek]
---

## 1. 간단한 예
이름 스트림이 있고 이를 콘솔에 인쇄하려고 한다.

`peek()`는 `Consumer<T>`를 유일한 인수로 기대하므로 적합해 보인다.

```java
Stream<String> nameStream = Stream.of("Alice", "Bob", "Chuck");
nameStream.peek(System.out::println);
```

그러나 위의 코드 조각은 출력을 생성하지 않는다. 이유를 이해하기 위해 스트림 수명주기 측면을 확인한다.

## 2. Intermediate와 Terminal Operations
스트림은 데이터 소스, 0개 이상의 중간 작업, 0개 또는 1개의 터미널 작업이라는 세 부분으로 구성된다.

소스는 파이프라인에 요소를 제공한다.

중간 작업은 요소를 하나씩 가져와 처리한다. 모든 중간 작업은 게으르기 때문에 파이프라인이 작동하기 시작할 때까지 어떤 작업도 효과가 없다.

터미널 운영은 스트림 수명주기의 종료를 의미한다. 시나리오에서 가장 중요한 것은 파이프라인에서 작업을 시작한다는 것 이다.

## 3. peek() 사용법
첫 번째 예에서 `peek()`가 작동하지 않은 이유는 이것이 중간 작업이고 파이프라인에 터미널 작업을 적용하지 않았기 때문이다. 또는 `forEach()`를 동일한 인수와 함께 사용하여 원하는 동작을 얻을 수도 있다.

```java
Stream<String> nameStream = Stream.of("Alice", "Bob", "Chuck");
nameStream.forEach(System.out::println);
```

`peek()`의 [Javadoc 페이지](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/stream/Stream.html#peek(java.util.function.Consumer))에는 다음과 같이 나와 있다. "이 메소드는 주로 디버깅을 지원하기 위해 존재하며, 요소가 파이프라인의 특정 지점을 지나갈 때 요소를 확인하려는 경우이다."

동일한 Javadoc 페이지에 있는 다음 코드를 확인한다.

```java
Stream.of("one", "two", "three", "four")
  .filter(e -> e.length() > 3)
  .peek(e -> System.out.println("Filtered value: " + e))
  .map(String::toUpperCase)
  .peek(e -> System.out.println("Mapped value: " + e))
  .collect(Collectors.toList());
```

각 작업을 통과한 요소를 관찰하는 방법을 보여준다.

게다가 `peek()`는 다른 시나리오, 즉 요소의 내부 상태를 변경하려는 경우에도 유용할 수 있다. 예를 들어, 모든 사용자 이름을 인쇄하기 전에 소문자로 변환한다고 가정한다.

```java
Stream<User> userStream = Stream.of(new User("Alice"), new User("Bob"), new User("Chuck"));
userStream.peek(u -> u.setName(u.getName().toLowerCase()))
  .forEach(System.out::println);
```

`map()`을 사용할 수도 있지만 요소를 교체하고 싶지 않기 때문에 `peek()`가 더 편리하다.

## [출처 및 참고]
* [https://www.baeldung.com/java-streams-peek-api](https://www.baeldung.com/java-streams-peek-api)
