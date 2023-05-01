---
title: Java PriorityBlockingQueue
author: dejavuhyo
date: 2023-05-02 08:50:00 +0900
categories: [Language, Java]
tags: [java-priority-blocking-queue, priority-blocking-queue, 자바-큐, 큐]
---

## 1. 요소의 우선순위
표준 대기열과 달리 PriorityBlockingQueue에는 어떤 유형의 요소도 추가할 수 없다. 두 가지 옵션이 있다.

* [Comparable](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Comparable.html)을 구현하는 요소 추가

* [Comparator](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Comparator.html)도 제공한다는 조건으로 Comparable을 구현하지 않는 요소 추가

Comparator 또는 Comparable 구현을 사용하여 요소를 비교하면 PriorityBlockingQueue가 항상 정렬된다.

목표는 우선 순위가 가장 높은 요소가 항상 먼저 정렬되는 방식으로 비교 논리를 구현하는 것이다. 그런 다음 대기열에서 요소를 제거하면 항상 우선 순위가 가장 높은 요소가 된다.

먼저 대기열을 여러 스레드에서 사용하는 대신 단일 스레드에서 사용한다. 이렇게 하면 단위 테스트에서 요소의 순서를 쉽게 증명할 수 있다.

```java
PriorityBlockingQueue<Integer> queue = new PriorityBlockingQueue<>();
ArrayList<Integer> polledElements = new ArrayList<>();

queue.add(1);
queue.add(5);
queue.add(2);
queue.add(3);
queue.add(4);

queue.drainTo(polledElements);

assertThat(polledElements).containsExactly(1, 2, 3, 4, 5);
```

요소를 임의의 순서로 대기열에 추가하더라도 요소를 폴링하기 시작하면 순서가 지정된다. 이는 Integer 클래스가 Comparable을 구현하기 때문이며, 이는 순서대로 오름차순으로 대기열에서 꺼내는 데 사용된다.

또한 **두 요소가 비교되고 동일한 경우 순서가 어떻게 지정되는지에 대한 보장이 없다.**

## 2. Queue를 사용하여 차단
표준 대기열을 다루는 경우 `poll()`을 호출하여 요소를 검색한다. 그러나 대기열이 비어 있는 경우 `poll()`을 호출하면 null이 반환된다.

PriorityBlockingQueue는 BlockingQueue 인터페이스를 구현하여 빈 대기열에서 제거할 때 차단할 수 있는 몇 가지 추가 메서드를 제공한다. `take()` 메서드는 다음과 같다.

```java
PriorityBlockingQueue<Integer> queue = new PriorityBlockingQueue<>();

new Thread(() -> {
  System.out.println("Polling...");

  try {
      Integer poll = queue.take();
      System.out.println("Polled: " + poll);
  } catch (InterruptedException e) {
      e.printStackTrace();
  }
}).start();

Thread.sleep(TimeUnit.SECONDS.toMillis(5));
System.out.println("Adding to queue");
queue.add(1);
```

`sleep()`을 사용하는 것은 사물을 보여주는 약간 불안정한 방법이지만 이 코드를 실행하면 다음을 확인 할 수 있다.

```text
Polling...
Adding to queue
Polled: 1
```

이것은 항목이 추가될 때까지 `take()`가 차단되었음을 증명한다.

* 스레드는 시작되었음을 증명하기 위해 "Polling"을 출력한다.

* 그런 다음 테스트는 약 5초 동안 일시 중지되어 스레드가 이 지점에서 `take()`를 호출했음을 증명한다.

* 대기열에 추가하고 `take()`가 사용 가능하게 되자마자 요소를 반환했음을 증명하기 위해 거의 즉시 "Polled: 1"을 확인한다.

또한 BlockingQueue 인터페이스는 전체 대기열에 추가할 때 차단하는 방법도 제공한다.

그러나 PriorityBlockingQueue는 제한이 없다. 이것은 결코 가득 차지 않을 것이라는 것을 의미하므로 항상 새로운 요소를 추가할 수 있다.

## 3. Blocking과 Prioritization을 함께 사용
PriorityBlockingQueue의 두 가지 주요 개념을 설명했으므로 두 가지를 함께 사용한다. 이전 예제를 간단히 확장할 수 있지만 이번에는 큐에 더 많은 요소를 추가한다.

```java
Thread thread = new Thread(() -> {
    System.out.println("Polling...");
    while (true) {
        try {
            Integer poll = queue.take();
            System.out.println("Polled: " + poll);
        } 
        catch (InterruptedException e) { 
            e.printStackTrace();
        }
    }
});

thread.start();

Thread.sleep(TimeUnit.SECONDS.toMillis(5));
System.out.println("Adding to queue");

queue.addAll(newArrayList(1, 5, 6, 1, 2, 6, 7));
Thread.sleep(TimeUnit.SECONDS.toMillis(1));
```

이것은 `sleep()`을 사용하기 때문에 약간 불안정하지만 여전히 유효한 사용 사례를 보여준다. 이제 요소가 추가되기를 기다리며 차단하는 큐가 있다. 그런 다음 한 번에 많은 요소를 추가한 다음 우선순위에 따라 처리됨을 보여준다. 출력은 다음과 같다.

```text
Polling...
Adding to queue
Polled: 1
Polled: 1
Polled: 2
Polled: 5
Polled: 6
Polled: 6
Polled: 7
```

## [출처 및 참고]
* [https://www.baeldung.com/java-priority-blocking-queue](https://www.baeldung.com/java-priority-blocking-queue)
