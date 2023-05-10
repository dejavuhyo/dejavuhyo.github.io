---
title: Java ArrayDeque
author: dejavuhyo
date: 2023-02-07 09:00:00 +0900
categories: [Language, Java]
tags: [java-deque, array-deque, 자바-큐, 큐]
---

## 1. API 개요
각 작업에는 기본적으로 두 가지 옵션이 있다.

첫 번째 그룹은 작업이 실패하면 예외를 throw하는 메서드로 구성된다. 다른 그룹은 상태 또는 값을 반환한다.

| Operation | Method | Method throwing Exception |
|:-----:|:-----:|:-----:|
| Insertion from Head | offerFirst(e) | addFirst(e) |
| Removal from Head | pollFirst() | removeFirst() |
| Retrieval from Head | peekFirst() | getFirst() |
| Insertion from Tail | offerLast(e) | addLast(e) |
| Removal from Tail | pollLast() | removeLast() |
| Retrieval from Tail | peekLast() | getLast() |

## 2. Methods 사용
ArrayDeque를 사용하는 방법에 대한 몇 가지 예이다.

### 1) ArrayDeque를 스택으로 사용
클래스를 스택으로 취급하고 요소를 푸시하는 방법의 예이다.

```java
@Test
public void whenPush_addsAtFirst() {
    Deque<String> stack = new ArrayDeque<>();
    stack.push("first");
    stack.push("second");

    assertEquals("second", stack.getFirst());
}
```

스택으로 사용될 때 ArrayDeque에서 요소를 팝하는 방법이다.

```java
@Test
public void whenPop_removesLast() {
    Deque<String> stack = new ArrayDeque<>();
    stack.push("first");
    stack.push("second");
 
    assertEquals("second", stack.pop());
}
```

`pop()` 메서드는 스택이 비어 있을 때 NoSuchElementException을 발생시킨다.

### 2) ArrayDeque를 대기열로 사용
Queue로 사용될 때 ArrayDeque에서 요소를 제공하는 방법을 보여주는 예제이다.

```java
@Test
public void whenOffer_addsAtLast() {
    Deque<String> queue = new ArrayDeque<>();
    queue.offer("first");
    queue.offer("second");

    assertEquals("second", queue.getLast());
}
```

그리고 Queue로 사용될 때 ArrayDeque에서 요소를 폴링하는 방법이다.

```java
@Test
public void whenPoll_removesFirst() {
    Deque<String> queue = new ArrayDeque<>();
    queue.offer("first");
    queue.offer("second");

    assertEquals("first", queue.poll());
}
```

대기열이 비어 있으면 `poll()` 메서드는 null 값을 반환 한다.

## 3. ArrayDeque 구현 방식

![arraydeque](/assets/img/2023-05-10-java-array-deque/arraydeque.jpg)

내부적으로 ArrayDeque는 채워지면 크기가 두 배가 되는 배열로 지원된다.

처음에 배열은 크기 16으로 초기화된다. 두 개의 포인터, 즉 머리와 꼬리를 유지하는 양방향 대기열로 구현된다.

### 1) Stack의 ArrayDeque

![stack](/assets/img/2023-05-10-java-array-deque/stack.jpg)

사용자가 push 방식을 사용하여 요소를 추가하면 헤드 포인터가 하나씩 이동한다.

요소를 팝하면 요소가 가비지 수집될 수 있도록 헤드 위치의 요소를 null로 설정한 다음 헤드 포인터를 하나씩 뒤로 이동한다.

### 2) Queue의 ArrayDeque

![queue](/assets/img/2023-05-10-java-array-deque/queue.jpg)

offer 메서드를 사용하여 요소를 추가하면 꼬리 포인터가 하나씩 이동한다.

사용자가 요소를 폴링하면 요소가 가비지 수집될 수 있도록 헤드 위치의 요소를 null로 설정한 다음 헤드 포인터를 이동한다.

### 3) ArrayDeque 참고 사항
이 특정 구현에 대한 몇 가지 참고 사항이다.

* 스레드로부터 안전하지 않다.

* Null 요소는 허용되지 않는다.

* 동기화된 스택 보다 훨씬 빠르게 작동한다.

* 더 나은 참조 지역성으로 인해 LinkedList 보다 빠른 대기열이다.

* 대부분의 작업은 일정한 시간 복잡도를 상각했다.

* ArrayDeque에 의해 반환된 Iterator는 fail-fast이다.

* ArrayDeque는 요소를 추가하는 동안 헤드 포인터와 테일 포인터가 서로 만날 때 배열의 크기를 자동으로 두 배로 늘린다.

## [출처 및 참고]
* [https://www.baeldung.com/java-array-deque](https://www.baeldung.com/java-array-deque)
