---
title: Java 문자열을 문자 스트림으로 변환
author: dejavuhyo
date: 2023-12-04 23:15:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-string, 자바-스트림, 스트림-변환, 스트림-문자열]
---

## 1. chars()를 이용한 변환
String API에는 String 개체에서 S 스트림의 인스턴스를 얻을 수 있는 `chars()`라는 새로운 메서드가 있다. 이 간단한 API는 입력 String에서 Int Stream 인스턴스를 반환한다.

간단히 말해서 IntStream에는 String 객체의 문자에 대한 정수 표현이 포함되어 있다.

```java
String testString = "String";
IntStream intStream = testString.chars();
```

문자를 동등한 문자로 변환하지 않고 문자의 정수 표현으로 작업하는 것이 가능하다. 각 정수를 Character 객체에 넣을 필요가 없기 때문에 약간의 성능 향상이 있을 수 있다.

그러나 읽기 위해 문자를 표시하려면 정수를 인간에게 친숙한 문자 형식으로 변환해야 한다.

```java
Stream<Character> characterStream = testString.chars().mapToObj(c -> (char) c);
```

## 2. codePoints()를 사용한 변환
`codePoints()`` 메서드를 사용하여 문자열에서 IntStream 인스턴스를 가져올 수 있다. 이 API를 사용하면 유니코드 보조 문자를 효과적으로 처리할 수 있다는 장점이 있다.

보조 문자는 유니코드 서로게이트 쌍으로 표시되며 단일 코드 포인트로 병합된다. 이렇게 하면 모든 유니코드 기호를 올바르게 처리(및 표시)할 수 있다.

```java
IntStream intStream1 = testString.codePoints();
```

반환된 IntStream을 `Stream<Character>` 에 매핑하여 사용자에게 표시 해야 한다.

```java
Stream<Character> characterStream2 = testString.codePoints().mapToObj(c -> (char) c);
```

## 3. 단일 문자열 스트림으로 변환
지금까지 문자 스트림을 얻을 수 있었다. 대신 단일 문자 String의 스트림을 원하면 `codePoints()` 또는 `chars()` 메서드를 사용하여 이제 `Stream<String>` 에 매핑할 수 있는 IntStream 인스턴스를 얻는다.

매핑 프로세스에는 먼저 정수 값을 해당하는 문자 값으로 변환하는 작업이 포함된다.

그런 다음 `String.valueOf()` 또는 `Character.toString()`을 사용하여 문자를 String 객체로 변환할 수 있다.

```java
Stream<String> stringStream = testString.codePoints()
  .mapToObj(c -> String.valueOf((char) c));
```

## [출처 및 참고]
* [https://www.baeldung.com/java-string-to-stream](https://www.baeldung.com/java-string-to-stream)
