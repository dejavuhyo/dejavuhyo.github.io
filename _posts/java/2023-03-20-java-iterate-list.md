---
title: Java List 반복
author: dejavuhyo
date: 2023-03-20 08:40:00 +0900
categories: [Language, Java]
tags: [java-list, java-iterate-list, iterate-list, java-iterate, 자바-리스트, 자바-리스트-반복, 리스트-반복]
---

## 1. for Loop
몇 가지 for 루프 옵션이다.

예를 들어 국가 목록을 정의한다.

```java
List<String> countries = Arrays.asList("Germany", "Panama", "Australia");
```

### 1) 기본 for Loop
반복을 위한 가장 일반적인 흐름 제어문은 기본 for 루프이다.

for 루프는 세미콜론으로 구분된 세 가지 유형의 명령문을 정의한다. 첫 번째 문은 초기화 문이다. 두 번째는 종료 조건을 정의한다. 마지막 문은 업데이트 절이다.

여기서는 단순히 정수 변수를 인덱스로 사용하고 있다.

```java
for (int i = 0; i < countries.size(); i++) {
    System.out.println(countries.get(i));
}
```

초기화에서 시작점을 지정하기 위해 정수 변수를 선언해야 한다. 이 변수는 일반적으로 목록 인덱스 역할을 한다.

종료 조건은 평가 후 boolean을 반환하는 표현식이다. 이 표현식이 거짓으로 평가되면 루프가 종료된다.

업데이트 절은 인덱스 변수의 현재 상태를 수정하여 종료 시점까지 늘리거나 줄이는 데 사용된다.

### 2) 향상된 for Loop
향상된 for 루프는 목록의 모든 요소를 방문할 수 있는 간단한 구조이다. 기본 for 루프와 유사하지만, 더 읽기 쉽고 간결하다. 결과적으로 목록을 순회하는 데 가장 일반적으로 사용되는 형식 중 하나이다.

향상된 for 루프는 기본 for 루프보다 간단하다.

```java
for (String country : countries) {
    System.out.println(country);
}
```

## 2. Iterators
`Iterator`는 내부 표현에 대해 걱정할 필요 없이 데이터 구조를 탐색할 수 있는 표준 인터페이스를 제공하는 디자인 패턴이다.

데이터 구조를 순회하는 이 방법은 많은 이점을 제공하며 그 중 코드가 구현에 의존하지 않는다는 점을 강조할 수 있다.

따라서 구조는 이진 트리 또는 이중 연결 목록이 될 수 있다. Iterator가 순회 수행 방식에서 우리를 추상화하기 때문이다. 이러한 방식으로 불쾌한 문제 없이 코드의 데이터 구조를 쉽게 대체할 수 있다.

### 1) Iterator
Java에서 `Iterator 패턴`은 `java.util.Iterator` 클래스에 반영된다. `Java Collections`에서 널리 사용된다. Iterator에는 `hasNext()` 및 `next()` 메서드라는 두 가지 주요 메서드가 있다.

두 메서드를 사용하는 방법이다.

```java
Iterator<String> countriesIterator = countries.iterator();

while(countriesIterator.hasNext()) {
    System.out.println(countriesIterator.next()); 
}
```

* `hasNext()` 메서드는 목록에 남아 있는 요소가 있는지 확인한다.

* `next()` 메서드는 반복의 다음 요소를 반환한다.

### 2) ListIterator
`ListIterator`를 사용하면 요소 목록을 정방향 또는 역방향으로 순회할 수 있다.

ListIterator로 목록을 앞으로 스크롤 하는 것은 Iterator에서 사용하는 것과 유사한 메커니즘을 따른다. 이런 식으로 `next()` 메서드를 사용하여 iterator를 앞으로 이동할 수 있고 `hasNext()` 메서드를 사용하여 목록의 끝을 찾을 수 있다.

ListIterator는 이전에 사용한 Iterator와 매우 유사하다.

```java
ListIterator<String> listIterator = countries.listIterator();

while(listIterator.hasNext()) {
    System.out.println(listIterator.next());
}
```

## 3. forEach()

### 1) Iterable.forEach()
Java 8부터 `forEach()` 메서드를 사용하여 list의 elements를 반복할 수 있다. 이 메소드는 Iterable 인터페이스에서 정의되며 `Lambda` 표현식을 매개변수로 허용할 수 있다.

```java
countries.forEach(System.out::println);
```

forEach 함수 이전에는 Java의 모든 반복자가 활성화되었다. 즉, 특정 조건이 충족될 때까지 데이터 수집을 순회하는 for 또는 while 루프가 포함되었다.

Iterable 인터페이스의 함수로 forEach가 도입되면서 Iterable을 구현하는 모든 클래스에 forEach 함수가 추가 되었다.

### 2) Stream.forEach()
또한 값 모음을 Stream으로 변환하고 `forEach()`, `map()` 및 `filter()`와 같은 작업에 액세스할 수 있다.

Stream의 일반적인 사용이다.

```java
countries.stream().forEach((c) -> System.out.println(c));
```

## [출처 및 참고]
* [https://www.baeldung.com/java-iterate-list](https://www.baeldung.com/java-iterate-list)
