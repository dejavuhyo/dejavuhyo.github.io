---
title: Java Enumeration과 Iterator의 차이점
author: dejavuhyo
date: 2022-10-18 10:00:00 +0900
categories: [Language, Java]
tags: [enumeration-and-iterator, java-enumeration, java-iterator, enumeration-vs-iterator, java-열거, java-반복자, 열거-반복자, 열거-반복자-차이점]
---

## 1. Enumeration
[Enumeration](https://docs.oracle.com/javase/7/docs/api/java/util/Enumeration.html)는 버전 1.0부터 Java에 있었다. 인터페이스이며 모든 구현을 통해 요소에 하나씩 액세스할 수 있다. 간단히 말해서 [Vector](https://docs.oracle.com/javase/8/docs/api/java/util/Vector.html) 및 [Hashtable](https://www.baeldung.com/java-hash-table)과 같은 개체 컬렉션을 반복하는데 사용된다.

Enumeration의 예이다.

```java
Vector<Person> people = new Vector<>(getPersons());
Enumeration<Person> enumeration = people.elements();
while (enumeration.hasMoreElements()) {
    System.out.println("First Name = " + enumeration.nextElement().getFirstName());
}
```

여기서는 Enumeration을 사용하여 Person의 firstName을 인쇄하고 있다. `elements()` 메소드는 Enumeration에 대한 참조를 제공하며 이를 사용 하여 요소에 하나씩 액세스할 수 있다.

## 2. Iterator
Iterator는 Java 1.2부터 존재했으며 동일한 버전에서도 도입된 컬렉션을 반복하는데 사용된다.

다음으로 Iterator를 사용하여 Person의 firstName을 인쇄한다. `iterator()`는 Iterator에 대한 참조를 제공하고 이를 사용하여 요소에 하나씩 액세스할 수 있다.

```java
List<Person> persons = getPersons();
Iterator<Person> iterator = persons.iterator();
while (iterator.hasNext()) {
    System.out.println("First Name = " + iterator.next().getFirstName());
}
```

따라서 Enumeration 및 Iterator는 각각 1.0 및 1.2부터 Java에 있으며 한 번에 하나씩 개체 컬렉션을 반복하는데 사용된다.

## 3. Enumeration와 Iterator의 차이점

| Enumeration | Iterator |
|:-----:|:-----:|
| Vector 및 Hashtable을 열거하기 위해 Java 1.0부터 존재한다. | List, Set, Map 등과 같은 컬렉션을 반복하기 위해 Java 1.2부터 존재한다. |
| `hasMoreElements()` 및 `nextElement()`의 두 가지 메서드가 포함되어 있다. | `hasNext()`, `next()` 및 `remove()`의 세 가지 메서드가 포함되어 있다. |
| 메소드의 이름이 길다. | 메소드에는 짧고 간결한 이름이 있다. |
| 반복하는 동안 요소를 제거하는 방법이 없다. | 반복하는 동안 요소를 제거하려면 `remove()`가 있어야 한다. |
| Java 9에 추가된 `asIterator()`는 Enumeration 상단에 Iterator를 제공한다. 그러나 이 특별한 경우에는 `remove()`가 UnsupportedOperationException을 발생시킨다. | Java 8에 추가된 `forEachRemaining()`은 나머지 요소에 대한 작업을 수행한다. |

## [출처 및 참고]
* [https://www.baeldung.com/java-enumeration-vs-iterator](https://www.baeldung.com/java-enumeration-vs-iterator)
