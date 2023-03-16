---
title: Java List에서 첫 번째 Elememt 제거
author: dejavuhyo
date: 2023-03-16 11:10:00 +0900
categories: [Language, Java]
tags: [java-list, java-remove-first-element, java-list-element, list-remove-first-element, list-remove-element, remove-element, 자바-리스트, 리스트-엘리먼트-제거, 엘리먼트-제거, 자바-리스트-엘리먼트]
---

## 1. List 만들기
먼저 List를 채운다.

```java
@Before
public void init() {
    list.add("cat");
    list.add("dog");
    list.add("pig");
    list.add("cow");
    list.add("goat");

    linkedList.add("cat");
    linkedList.add("dog");
    linkedList.add("pig");
    linkedList.add("cow");
    linkedList.add("goat");
}
```

## 2. ArrayList
`ArrayList`에서 첫 번째 요소를 제거 하고 목록에 더 이상 포함되지 않도록 한다.

```java
@Test
public void givenList_whenRemoveFirst_thenRemoved() {
    list.remove(0);

    assertThat(list, hasSize(4));
    assertThat(list, not(contains("cat")));
}
```

첫 번째 요소를 제거하기 위해 `remove(index)` 메서드를 사용하고 있다. 이는 List 인터페이스의 모든 구현에서도 작동한다.

## 3. LinkedList
`LinkedList`는 자체 방식으로 `remove(index)` 메서드도 구현하지만 `removeFirst()` 메서드도 있다.

```java
@Test
public void givenLinkedList_whenRemoveFirst_thenRemoved() {
    linkedList.removeFirst();

    assertThat(linkedList, hasSize(4));
    assertThat(linkedList, not(contains("cat")));
}
```

## 4. 시간 복잡도
방법은 비슷해 보이지만 효율성은 다르다. `ArrayList`의 `remove()` 메서드는 `O(n)` 시간이 필요하지만 `LinkedList`의 `removeFirst()` 메서드는 `O(1)` 시간이 필요하다.

이는 `ArrayList`가 내부적으로 배열을 사용하고 `remove()` 작업을 수행하려면 배열의 나머지 부분을 시작 부분에 복사해야 하기 때문이다. 배열이 클수록 더 많은 요소를 이동해야 한다.

이와 달리 `LinkedList`는 각 요소가 다음 요소와 이전 요소를 가리키는 포인터를 사용한다.

따라서 첫 번째 요소를 제거한다는 것은 첫 번째 요소에 대한 포인터를 변경하는 것을 의미한다. 이 작업은 목록의 크기에 의존하지 않고 항상 같은 시간이 필요하다.

## [출처 및 참고]
* [https://www.baeldung.com/java-remove-first-element-from-list](https://www.baeldung.com/java-remove-first-element-from-list)
