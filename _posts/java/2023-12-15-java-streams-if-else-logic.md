---
title: Java 스트림 if/else 로직
author: dejavuhyo
date: 2023-12-15 19:35:00 +0900
categories: [Language, Java]
tags: [java-stream, streams-if, streams-else, if-else, 자바-스트림, 스트림-if, 스트림-else]
---

## 1. forEach() 내의 기존 if/else 로직
먼저 정수 목록을 만든 다음 정수 스트림 `forEach()` 메서드 내에서 기존 `if/else` 로직을 사용한다.

```java
List<Integer> ints = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

ints.stream()
    .forEach(i -> {
        if (i.intValue() % 2 == 0) {
            Assert.assertTrue(i.intValue() % 2 == 0);
        } else {
            Assert.assertTrue(i.intValue() % 2 != 0);
        }
    });
```

forEach 메소드에는 Java 모듈러스 연산자를 사용하여 정수가 홀수인지 짝수인지 확인하는 `if-else` 로직이 포함되어 있다.

## 2. filter()를 사용한 if/else 로직
`Stream filter()` 메서드를 사용하여 보다 쉬운 구현을 살펴본다.

```java
Stream<Integer> evenIntegers = ints.stream()
    .filter(i -> i.intValue() % 2 == 0);
Stream<Integer> oddIntegers = ints.stream()
    .filter(i -> i.intValue() % 2 != 0);

evenIntegers.forEach(i -> Assert.assertTrue(i.intValue() % 2 == 0));
oddIntegers.forEach(i -> Assert.assertTrue(i.intValue() % 2 != 0));
```

위에서는 정수 목록을 두 개의 Stream으로 분리하기 위해 `Stream filter()` 메서드를 사용하여 `if/else` 로직을 구현했다. 하나는 짝수 정수용이고 다른 하나는 홀수 정수용이다.

## [출처 및 참고]
* [https://www.baeldung.com/java-8-streams-if-else-logic](https://www.baeldung.com/java-8-streams-if-else-logic)
