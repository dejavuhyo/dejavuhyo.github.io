---
title: Java Arrays.asList와 new ArrayList(Arrays.asList()) 차이점
author: dejavuhyo
date: 2023-05-26 13:00:00 +0900
categories: [Language, Java]
tags: [java-arrays, arrays-aslist, new-arraylist, java-list, 자바-어레이, 자바-리스트]
---

## 1. Arrays.asList
[Arrays.asList](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Arrays.html#asList(T...)) 방법을 사용하여 배열을 고정 크기의 List 객체로 변환할 수 있다. 이 목록은 배열을 목록으로 사용할 수 있도록 하는 래퍼일 뿐이다. 데이터가 복사되거나 생성되지 않는다.

또한 요소 추가 또는 제거가 허용되지 않기 때문에 길이를 수정할 수 없다.

그러나 배열 내부의 단일 항목을 수정할 수 있다. List의 단일 항목에 대한 모든 수정 사항은 원래 배열에 반영된다.

```java
String[] stringArray = new String[] { "A", "B", "C", "D" };
List stringList = Arrays.asList(stringArray);
```

이제 stringList의 첫 번째 요소를 수정한다.

```java
stringList.set(0, "E");
 
assertThat(stringList).containsExactly("E", "B", "C", "D");
assertThat(stringArray).containsExactly("E", "B", "C", "D");
```

원래 배열도 수정되었다. 목록과 배열 모두 동일한 순서로 정확히 동일한 요소를 포함한다.

이제 stringList에 새 요소를 삽입한다.

```java
stringList.add("F");
```

```text
java.lang.UnsupportedOperationException
	at java.base/java.util.AbstractList.add(AbstractList.java:153)
	at java.base/java.util.AbstractList.add(AbstractList.java:111)
```

List에 요소를 추가/제거하면 `java.lang.UnsupportedOperationException`이 발생한다.

## 2. ArrayList(Arrays.asList(array))
`Arrays.asList` 메서드와 유사하게 `ArrayList<>(Arrays.asList(array))`를 사용하여 배열에서 목록을 생성할 수 있다.

그러나 이전 예제와 달리 이것은 배열의 독립적인 복사본이므로 새 목록을 수정해도 원래 배열에는 영향을 미치지 않는다. 또한 요소 추가 및 제거와 같은 일반 ArrayList의 모든 기능이 있다.

```java
String[] stringArray = new String[] { "A", "B", "C", "D" }; 
List stringList = new ArrayList<>(Arrays.asList(stringArray));
```

stringList의 첫 번째 요소를 수정한다.

```java
stringList.set(0, "E");

assertThat(stringList).containsExactly("E", "B", "C", "D");
```

원래 어레이의 변화를 살펴본다.

```java
assertThat(stringArray).containsExactly("A", "B", "C", "D");
```

원래 배열은 그대로 유지된다.

JDK 소스 코드를 살펴보면 `Arrays.asList` 메서드가 `java.util.ArrayList`와 다른 ArrayList 유형을 반환하는 것을 볼 수 있다. 주요 차이점은 반환된 ArrayList가 기존 배열만 래핑한다는 것이다. 추가 및 제거 메서드는 구현하지 않는다.

## [출처 및 참고]
* [https://www.baeldung.com/java-arrays-aslist-vs-new-arraylist](https://www.baeldung.com/java-arrays-aslist-vs-new-arraylist)
