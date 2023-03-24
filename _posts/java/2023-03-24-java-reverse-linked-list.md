---
title: Java Linked List 뒤집기
author: dejavuhyo
date: 2023-03-24 09:15:00 +0900
categories: [Language, Java]
tags: [java-list, java-linked-list, linked-list, reverse-linked-list, 자바-리스트, 자바-링크드-리스트, 링크드-리스트-뒤집기, 링크드-리스트]
---

## 1. Linked List 데이터 구조
Linked List는 각 요소의 포인터가 순서를 결정하는 선형 데이터 구조이다. 연결된 목록의 각 요소에는 목록 데이터를 저장하는 데이터 필드와 시퀀스의 다음 요소를 가리키는 포인터 필드가 있다. 또한 헤드 포인터를 사용하여 연결된 목록의 시작 요소를 가리킬 수 있다.

![linked-list](/assets/img/2023-03-24-java-reverse-linked-list/linked-list.png)

연결 리스트를 뒤집은 후 헤드는 원래 연결 리스트의 마지막 요소를 가리키고 각 요소의 포인터는 원래 연결 리스트의 이전 요소를 가리킨다.

![reversed-linked-list](/assets/img/2023-03-24-java-reverse-linked-list/reversed-linked-list.png)

Java에는 List 및 Deque 인터페이스의 이중 연결 목록 구현을 제공하는 LinkedList 클래스가 있다. 그러나 여기에서는 일반적인 단일 연결 목록 데이터 구조를 사용한다.

Linked List의 요소를 나타내는 ListNode 클래스이다.

```java
public class ListNode {

    private int data;
    private ListNode next;

    ListNode(int data) {
        this.data = data;
        this.next = null;
    }

   // standard getters and setters
}
```

ListNode 클래스에는  두 개의 필드가 있다.

* 요소의 데이터를 나타내는 정숫값

* 다음 요소에 대한 포인터/참조

연결된 목록에는 여러 ListNode 개체가 포함될 수 있다. 예를 들어 루프를 사용하여 위의 샘플 연결 목록을 구성할 수 있다.

```java
ListNode constructLinkedList() {
    ListNode head = null;
    ListNode tail = null;
    for (int i = 1; i <= 5; i++) {
        ListNode node = new ListNode(i);
        if (head == null) {
            head = node;
        } else {
            tail.setNext(node);
        }
        tail = node;
    }
    return head;
}
```

## 2. 반복 알고리즘 구현
Java에서 반복 알고리즘을 구현한다.

```java
ListNode reverseList(ListNode head) {
    ListNode previous = null;
    ListNode current = head;
    while (current != null) {
        ListNode nextElement = current.getNext();
        current.setNext(previous);
        previous = current;
        current = nextElement;
    }
    return previous;
}
```

이 반복 알고리즘에서는 두 개의 ListNode 변수인 previous 및 current를 사용하여 연결된 목록에서 인접한 두 요소를 나타낸다. 반복할 때마다 이 두 요소를 뒤집은 다음 두 요소로 이동한다.

결국 현재 포인터는 null이 되고 이전 포인터는 이전 연결 목록의 마지막 요소가 된다. 따라서 이전은 역방향 연결 목록의 새 헤드 포인터이기도 하며 메서드에서 반환한다.

간단한 단위 테스트를 통해 이 반복 구현을 확인할 수 있다.

```java
@Test
void givenLinkedList_whenIterativeReverse_thenOutputCorrectResult() {
    ListNode head = constructLinkedList();
    ListNode node = head;
    for (int i = 1; i <= 5; i++) {
        assertNotNull(node);
        assertEquals(i, node.getData());
        node = node.getNext();
    }
 
    LinkedListReversal reversal = new LinkedListReversal();
    node = reversal.reverseList(head);
 
    for (int i = 5; i >= 1; i--) {
        assertNotNull(node);
        assertEquals(i, node.getData());
        node = node.getNext();
    }
}
```

단위 테스트에서는 먼저 5개의 노드가 있는 샘플 연결 목록을 구성합니다. 또한 연결된 목록의 각 노드에 올바른 데이터 값이 포함되어 있는지 확인합니다. 그런 다음 iterative 함수를 호출하여 연결된 목록을 뒤집습니다. 마지막으로 반전된 연결 목록을 확인하여 데이터가 예상대로 반전되었는지 확인합니다.

## 3. 재귀 알고리즘 구현
Java에서 재귀 알고리즘을 구현한다.

```java
ListNode reverseListRecursive(ListNode head) {
    if (head == null) {
        return null;
    }
    if (head.getNext() == null) {
        return head;
    }
    ListNode node = reverseListRecursive(head.getNext());
    head.getNext().setNext(head);
    head.setNext(null);
    return node;
}
```

reverseListRecursive 함수에서 마지막 요소에 도달할 때까지 연결된 목록의 각 요소를 재귀적으로 방문한다. 이 마지막 요소는 역방향 연결 목록의 새 헤드가 된다. 또한 부분적으로 반전된 연결 목록의 끝에 방문 요소를 추가한다.

마찬가지로 간단한 단위 테스트를 통해 이 재귀 구현을 확인할 수 있다.

```java
@Test
void givenLinkedList_whenRecursiveReverse_thenOutputCorrectResult() {
    ListNode head = constructLinkedList();
    ListNode node = head;
    for (int i = 1; i <= 5; i++) {
        assertNotNull(node);
        assertEquals(i, node.getData());
        node = node.getNext();
    }
 
    LinkedListReversal reversal = new LinkedListReversal();
    node = reversal.reverseListRecursive(head);
 
    for (int i = 5; i >= 1; i--) {
        assertNotNull(node);
        assertEquals(i, node.getData());
        node = node.getNext();
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-reverse-linked-list](https://www.baeldung.com/java-reverse-linked-list)
