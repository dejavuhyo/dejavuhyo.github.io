---
title: Java 컬렉션 압축
author: dejavuhyo
date: 2023-06-12 08:35:00 +0900
categories: [Language, Java]
tags: [java-collections-zip, collections-zip, 자바-컬렉션-압축, 컬렉션-압축]
---

## 1. 컬렉션 압축
두 개의 컬렉션을 하나의 논리적 컬렉션으로 압축하는 방법이다.

"zip" 작업은 표준 "concat" 또는 "merge"와 약간 다르다. "concat" 또는 "merge" 작업은 단순히 기존 컬렉션의 끝에 새 컬렉션을 추가하는 반면, "zip" 작업은 각 컬렉션에서 요소를 가져와 결합한다.

핵심 라이브러리는 "zip"을 암시적으로 지원하지 않지만 이 유용한 작업을 수행하는 타사 라이브러리가 분명히 있다.

하나는 사람들의 이름을 가지고 있고 다른 하나는 그들의 나이를 포함하는 두 개의 목록이다.

```java
List<String> names = new ArrayList<>(Arrays.asList("John", "Jane", "Jack", "Dennis"));

List<Integer> ages = new ArrayList<>(Arrays.asList(24, 25, 27));
```

압축 후, 두 컬렉션의 해당 요소로 구성된 이름-연령 쌍으로 끝난다.

## 2. 자바 8 IntStream 사용
핵심 Java를 사용하여 IntStream을 사용하여 인덱스를 생성한 다음 두 컬렉션에서 해당 요소를 추출하는 데 사용할 수 있다.

```java
IntStream
  .range(0, Math.min(names.size(), ages.size()))
  .mapToObj(i -> names.get(i) + ":" + ages.get(i))
  // ...
```

## 3. 구아바 스트림 사용
버전 21부터 Google Guava는 Streams 클래스에서 zip 도우미 메서드를 제공한다. 이렇게 하면 인덱스 생성 및 매핑의 번거로움이 제거되고 입력 및 작업에 대한 구문이 줄어든다.

```java
Streams
  .zip(names.stream(), ages.stream(), (name, age) -> name + ":" + age)
  // ...
```

## 4. jOOλ (jOOL) 사용
jOOL은 또한 Java 8 Lambda에 대한 몇 가지 매력적인 추가 기능을 제공하며 Tuple1에서 Tuple16 까지의 지원으로 zip 작업이 훨씬 더 흥미로워진다.

```java
Seq
  .of("John","Jane", "Dennis")
  .zip(Seq.of(24,25,27));
```

이렇게 하면 압축된 요소의 튜플을 포함하는 Seq의 결과가 생성된다.

```java
(tuple(1, "a"), tuple(2, "b"), tuple(3, "c"))
```

jOOL의 zip 방법은 사용자 지정 변환 기능을 제공할 수 있는 유연성을 제공한다.

```java
Seq
  .of(1, 2, 3)
  .zip(Seq.of("a", "b", "c"), (x, y) -> x + ":" + y);
```

또는 인덱스로만 압축하려는 경우 jOOL에서 제공하는 zipWithIndex 메서드를 사용할 수 있다.

```java
Seq.of("a", "b", "c").zipWithIndex();
```

## [출처 및 참고]
* [https://www.baeldung.com/java-collections-zip](https://www.baeldung.com/java-collections-zip)
