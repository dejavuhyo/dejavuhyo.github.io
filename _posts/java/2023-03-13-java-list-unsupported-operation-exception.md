---
title: Java List UnsupportedOperationException
author: dejavuhyo
date: 2023-03-13 20:15:00 +0900
categories: [Language, Java]
tags: [java-list, list-unsupported-operation-exception, unsupported-operation-exception, 자바-리스트, 리스트-unsupported-operation-exception]
---

## 1. UnsupportedOperationException
`java.util.List`에는 배열이 지원할 수 있는 일반적인 것보다 더 많은 기능이 있다. 예를 들어 하나의 기본 제공 메서드 호출만으로 특정 요소가 구조 내부에 있는지 확인할 수 있다. 이것이 일반적으로 배열을 `List` 또는 `Collection`으로 변환해야 하는 이유이다.

이 오류가 자주 발생하는 방법은 `java.util.Arrays`에서 `asList()` 메서드를 사용할 때이다.

```java
public static List asList(T... a)
```

다음을 반환한다.

* 주어진 배열의 크기에 따른 고정 크기 목록

* 원래 배열에 있는 것과 동일한 유형의 요소이며 객체여야 함

* 원래 배열과 같은 순서로 요소

* 직렬화 가능하고 `RandomAccess`를 구현하는 목록

`T`는 `varargs`이므로 배열이나 항목을 매개변수로 직접 전달할 수 있으며 메서드는 고정 크기 초기화 목록을 생성한다.

```java
List<String> flowers = Arrays.asList("Ageratum", "Allium", "Poppy", "Catmint");
```

실제 배열을 전달할 수도 있다.

```java
String[] flowers = { "Ageratum", "Allium", "Poppy", "Catmint" };
List<String> flowerList = Arrays.asList(flowers);
```

**반환된 List는 고정 크기 List이므로 요소를 추가/제거할 수 없다.**

더 많은 요소를 추가하려고 하면 `UnsupportedOperationException`이 발생한다.

```java
String[] flowers = { "Ageratum", "Allium", "Poppy", "Catmint" }; 
List<String> flowerList = Arrays.asList(flowers); 
flowerList.add("Celosia");
```

이 `Exception`의 근본 원인은 반환된 객체가 `java.util.ArrayList`와 동일하지 않기 때문에 `add()` 작업을 구현하지 않는다는 것이다.

`java.util.Arrays`의 `ArrayList` 이다.

동일한 예외를 얻는 또 다른 방법은 얻은 목록에서 요소를 제거하는 것이다.

반면에 필요할 때 변경할 수 있는 목록을 얻을 방법이 있다.

그중 하나는 `asList()`의 결과에서 직접 ArrayList 또는 모든 종류의 목록을 만드는 것이다.

```java
String[] flowers = { "Ageratum", "Allium", "Poppy", "Catmint" }; 
List<String> flowerList = new ArrayList<>(Arrays.asList(flowers));
```

## [출처 및 참고]
* [https://www.baeldung.com/java-list-unsupported-operation-exception](https://www.baeldung.com/java-list-unsupported-operation-exception)
