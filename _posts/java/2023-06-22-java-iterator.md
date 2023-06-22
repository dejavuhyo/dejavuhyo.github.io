---
title: Java Iterator
author: dejavuhyo
date: 2023-06-22 09:10:00 +0900
categories: [Language, Java]
tags: [java-iterator, iterator, 자바-반복자, 반복자]
---

## 1. Iterator 인터페이스
Collection에서 Iterator를 가져와 사용한다. 이는 `iterator()` 메서드를 호출하여 수행된다.

단순화를 위해 목록에서 Iterator 인스턴스를 얻는다.

```java
List<String> items = ...
Iterator<String> iter = items.iterator();
```

Iterator 인터페이스에는 세 가지 핵심 메서드가 있다.

### 1) hasNext()
`hasNext()` 메서드는 반복할 요소가 하나 이상 남아 있는지 확인하는 데 사용할 수 있다.

while 루프에서 조건으로 사용하도록 설계되었다.

```java
while (iter.hasNext()) {
    // ...
}
```

### 2) next()
`next()` 메서드는 다음 요소를 건너뛰고 가져오는 데 사용할 수 있다.

```java
String next = iter.next();
```

*`next()`를 호출하기 전에 `hasNext()`를 사용하는 것이 좋다.*

컬렉션의 반복자는 특정 구현에서 제공하지 않는 한 특정 순서로 반복을 보장하지 않는다.

### 3) remove()
컬렉션에서 현재 요소를 제거 하려면 `remove()`를 사용할 수 있다.

```java
iter.remove();
```

이는 ConcurrentModificationException의 위험 없이 컬렉션을 반복하는 동안 요소를 제거하는 안전한 방법이다.

### 4) 전체 Iterator 예제
결합하고 컬렉션 필터링을 위해 세 가지 방법이 있다.

```java
while (iter.hasNext()) {
    String next = iter.next();
    System.out.println(next);
 
    if( "TWO".equals(next)) {
        iter.remove();
    }
}
```

Iterator를 일반적으로 사용하는 방법이다. 다른 요소가 있는지 미리 확인하고 검색한 다음 그것에 대해 몇 가지 작업을 수행한다.

### 5) Lambda 식으로 반복
모든 요소를 ​​살펴보고 관련 작업을 수행하려는 경우 Iterator를 사용하는 것은 매우 장황하다.

Java 8부터 나머지 요소를 처리하는데 람다를 사용할 수 있는 forEachRemaining 메서드가 있다.

```java
iter.forEachRemaining(System.out::println);
```

## 2. ListIterator 인터페이스
ListIterator는 목록을 반복하기 위한 새로운 기능을 추가하는 확장이다.

```java
ListIterator<String> listIterator = items.listIterator(items.size());
```

이 경우 목록의 끝인 시작 위치를 어떻게 제공할 수 있는지 주의한다.

### 1) hasPrevious()와 previous()
ListIterator는 역순회에 사용할 수 있으므로 `hasNext()` 및 `next()`와 동등한 기능을 제공한다.

```java
while(listIterator.hasPrevious()) {
    String previous = listIterator.previous();
}
```

### 2) nextIndex()와 previousIndex()
실제 요소가 아닌 인덱스를 탐색할 수 있다.

```java
String nextWithIndex = items.get(listIterator.nextIndex());
String previousWithIndex = items.get(listIterator.previousIndex());
```

이는 현재 수정 중인 개체의 인덱스를 알아야 하거나 제거된 요소의 기록을 유지하려는 경우에 매우 유용할 수 있다.

### 3) add()
add 메서드는 이름에서 알 수 있듯이 `next()`가 반환하는 항목 앞 과 `previous()`가 반환하는 항목 뒤에 요소를 추가할 수 있다.

```java
listIterator.add("FOUR");
```

### 4) set()
`set()`은 `next()` 또는 `previous()` 호출에서 반환된 요소를 대체할 수 있다.

```java
String next = listIterator.next();
if( "ONE".equals(next)) {
    listIterator.set("SWAPPED");
}
```

*`add()` 또는 `remove()`를 호출하지 않은 경우에만 실행할 수 있다.*

### 5) 전체 ListIterator 예제

```java
ListIterator<String> listIterator = items.listIterator();
while(listIterator.hasNext()) {
    String nextWithIndex = items.get(listIterator.nextIndex());
    String next = listIterator.next();
    if("REPLACE ME".equals(next)) {
        listIterator.set("REPLACED");
    }
}
listIterator.add("NEW");
while(listIterator.hasPrevious()) {
    String previousWithIndex
     = items.get(listIterator.previousIndex());
    String previous = listIterator.previous();
    System.out.println(previous);
}
```

List에서 ListIterator를 가져오는 것으로 시작한 다음 인덱스(반복기의 내부 현재 요소를 증가시키지 않음  또는 next를 호출하여 다음 요소를 얻을 수 있다.

그런 다음 set으로 특정 항목을 교체 하고 add로 새 항목을 삽입 할 수 있다.

반복이 끝나면 뒤로 이동하여 추가 요소를 수정하거나 단순히 아래에서 위로 인쇄할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-iterator](https://www.baeldung.com/java-iterator)
