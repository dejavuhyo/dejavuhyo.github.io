---
title: Java LinkedList
author: dejavuhyo
date: 2023-03-02 13:05:00 +0900
categories: [Language, Java]
tags: [java-linkedlist, linkedlist, java-list, list, 자바-링크드리스트, 링크드리스트, 자바-리스트, 리스트]
---

## 1. LinkedList
LinkedList는 List 및 Deque 인터페이스의 이중 연결 목록 구현이다. 모든 선택적 목록 작업을 구현하고 모든 요소(null 포함)를 허용한다.

## 2. 특징
LinkedList의 가장 중요한 속성이다.

* 목록을 인덱싱하는 작업은 시작 또는 끝 중 지정된 인덱스에 가까운 쪽부터 목록을 순회한다.

* 동기화되지 않는다.

* 그것의 Iterator 및 ListIterator 반복자는 fail-fast이다. (즉, 반복자가 생성된 후 목록이 수정되면 ConcurrentModificationException이 발생함)

* 모든 요소는 다음 및 이전 항목에 대한 참조를 유지하는 노드이다.

* 삽입 순서를 유지한다.

LinkedList가 동기화되지는 않았지만, 다음과 같이 `Collections.synchronizedList` 메서드 를 호출하여 동기화된 버전을 검색할 수 있다.

## 3. ArrayList와 비교
둘 다 List 인터페이스를 구현하지만, 의미론이 다르므로 사용할 것을 결정하는 데 확실히 영향을 미친다.

### 1) 구조
ArrayList는 Array가 지원하는 인덱스 기반 데이터 구조이다. O(1)과 동일한 성능으로 해당 요소에 대한 임의 액세스를 제공한다.

### 2) 운영
항목의 삽입, 추가 및 제거 작업은 요소가 컬렉션 내의 임의의 위치에 추가될 때 배열 크기를 조정하거나 인덱스를 업데이트할 필요가 없기 때문에 LinkedList에서 더 빠르다. 주변 요소의 참조만 변경된다.

### 3) 메모리 사용량
LinkedList는 LinkedList의 모든 노드가 이전 요소와 다음 요소에 대한 두 개의 참조를 저장하는 반면 ArrayList는 데이터와 인덱스만 보유하므로 Array List보다 더 많은 메모리를 사용한다.

## 4. 사용법

### 1) Creation

```java
LinkedList<Object> linkedList = new LinkedList<>();
```

### 2) 요소 추가
LinkedList는 표준 `add()` 및 `addAll()` 메서드 외에 각각 시작 또는 끝에 요소를 추가하는 `addFirst()` 및 `addLast()`를 찾을 수 있는 List 및 Deque 인터페이스를 구현한다.

### 3) 요소 제거
요소 추가와 유사하게 이 목록 구현은 `removeFirst()` 및 `removeLast()`를 제공한다.

또한 boolean(컬렉션에 지정된 요소가 포함된 경우 true)을 반환하는 편리한 메서드 `removeFirstOccurence()` 및 `removeLastOccurence()`가 있다.

### 4) Queue Operations
Deque 인터페이스는 큐와 유사한 동작을 제공한다(실제로 Deque는 Queue 인터페이스를 확장함).

```java
linkedList.poll();
linkedList.pop();
```

이러한 메서드는 첫 번째 요소를 검색하고 목록에서 제거한다.

`poll()`과 `pop()`의 차이점은 pop은 빈 리스트에서 `NoSuchElementException()`을 발생시키는 반면 poll은 null을 반환한다는 것이다. `pollFirst()` 및 `pollLast()` API도 사용할 수 있다.

예를 들어 푸시 API가 작동 하는 방식은 다음과 같다.

```java
linkedList.push(Object o);
```

요소를 컬렉션의 헤드로 삽입한다.

LinkedList에는 다른 많은 메서드가 있으며 대부분 이미 Lists를 사용한 사용자에게 친숙할 것이다. Deque에서 제공하는 다른 방법은 "표준" 방법에 대한 편리한 대안이 될 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-linkedlist](https://www.baeldung.com/java-linkedlist)
* [https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/LinkedList.html](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/LinkedList.html)
