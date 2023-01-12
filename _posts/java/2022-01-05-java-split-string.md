---
title: Java 문자열 분할
author: dejavuhyo
date: 2022-01-05 09:40:00 +0900
categories: [Language, Java]
tags: [java-split-string, java-split, split-string, split, 자바-문자열-분할, 문자열-분할]
---

## 1. String.split()
String 클래스 자체는 split() 메서드를 제공한다. 이는 대부분의 시나리오에 매우 편리하고 충분하다. 단순히 구분 기호를 기반으로 주어진 String을 분할하여 Strings 배열을 반환한다.

몇 가지 예이다. 쉼표로 나누는 것이다.

```java
String[] splitted = "peter,james,thomas".split(",");
```

공백으로 나눈다.

```java
String[] splitted = "car jeep scooter".split(" ");
```

또한 점으로 나눈다.

```java
String[] splitted = "192.168.1.178".split("\\.");
```

정규식을 통해 쉼표, 공백 및 하이픈과 같은 여러 문자로 분할한다.

```java
String[] splitted = "b a, e, l.d u, n g".split("\\s+|,\\s*|\\.\\s*"));
```

## 2. StringUtils.split()
Apache의 common lang 패키지는 공백을 기본 구분 기호로 사용하여 분할 하는 null-safe split() 메서드 가 포함된 StringUtils 클래스를 제공한다.

```java
String[] splitted = StringUtils.split("car jeep scooter");
```

또한 추가 공백을 무시한다.

```java
String[] splitted = StringUtils.split("car   jeep  scooter");
```

## 3. Splitter.split()
Guava에도 Splitter fluent API가 있다.

```java
List<String> resultList = Splitter.on(',')
  .trimResults()
  .omitEmptyStrings()
  .splitToList("car,jeep,, scooter");
```

## 4. Split 및 Trim
때때로 주어진 문자열에는 구분 기호 주위에 일부 선행, 후행 또는 추가 공백이 포함된다. 입력 분할 결과 트리밍을 한 번에 처리하는 방법이다.

입력으로 다음과 같이 가정한다.

```java
String input = " car , jeep, scooter ";
```

구분 기호 앞 and/or 뒤에 여분의 공백을 제거하려면 정규식을 사용하여 분할 및 다듬기를 수행할 수 있다.

```java
String[] splitted = input.trim().split("\\s*,\\s*");
```

여기에서 trim() 메서드는 입력 문자열에서 선행 및 후행 공백을 제거하고 정규식 자체가 구분 기호 주위의 추가 공백을 처리한다.

Java 8 Stream 기능을 사용하여 동일한 결과를 얻을 수 있다.

```java
String[] splitted = Arrays.stream(input.split(","))
  .map(String::trim)
  .toArray(String[]::new);
```

> 일반적으로 String.split()이면 충분하다. 그러나 더 복잡한 경우에는 Apache의 commons-lang 기반 StringUtils 클래스 또는 깔끔하고 유연한 Guava API를 사용할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-split-string](https://www.baeldung.com/java-split-string)
