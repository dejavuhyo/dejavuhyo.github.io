---
title: Java Queue
author: dejavuhyo
date: 2023-05-11 09:45:00 +0900
categories: [Language, Java]
tags: [java-deque, array-deque, 자바-큐, 큐]
---

## 1. 대기열 시각화
Java의 대기열은 Queue를 선언한 후 뒤에 새 요소를 추가하고 앞에서 제거할 수 있다.

실제로 Java에서 접하게 될 **대부분의 대기열은 선입 선출 방식으로 작동하며 종종 FIFO로 약칭된다.**

## 2. 핵심 방법
Queue는 모든 구현 클래스에서 코딩해야 하는 여러 메서드를 선언한다.

* `offer()` - Queue에 새 요소를 삽입한다.

* `poll()` - Queue 앞의 요소를 제거한다.

* `peek()` - 제거하지 않고 Queue 앞에 있는 요소를 검사한다.

## 3. 추상 대기열
[AbstractQueue](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/AbstractQueue.html)는 Java가 제공하는 가장 간단한 Queue 구현 이다. 여기에는 offer를 제외한 일부 Queue 인터페이스 메서드 일부 구현이 포함된다.

AbstractQueue 클래스를 확장하는 사용자 지정 대기열을 생성할 때, null 요소 삽입을 허용 하지 않는 제공 메서드의 구현을 제공해야 한다.

또한 peek, poll, size 및 java.util의 iterator 메서드를 제공해야 한다.

AbstractQueue를 사용하여 간단한 Queue 구현을 만든다.

먼저 Queue의 요소를 저장하기 위해 LinkedList로 클래스를 정의한다.

```java
public class CustomBaeldungQueue<T> extends AbstractQueue<T> {

    private LinkedList<T> elements;

    public CustomBaeldungQueue() {
      this.elements = new LinkedList<T>();
    }
}
```

다음으로 필요한 메서드를 재정의하고 코드를 제공한다.

```java
@Override
public Iterator<T> iterator() {
    return elements.iterator();
}

@Override
public int size() {
    return elements.size();
}

@Override
public boolean offer(T t) {
    if(t == null) return false;
    elements.add(t);
    return true;
}

@Override
public T poll() {
    Iterator<T> iter = elements.iterator();
    T t = iter.next();
    if(t != null){
        iter.remove();
        return t;
    }
    return null;
}

@Override
public T peek() {
    return elements.getFirst();
}
```

단위 테스트로 작동하는지 확인한다.

```java
customQueue.add(7);
customQueue.add(5);

int first = customQueue.poll();
int second = customQueue.poll();

assertEquals(7, first);
assertEquals(5, second);
```

## 4. 하위 인터페이스
일반적으로 Queue 인터페이스는 3개의 기본 하위 인터페이스로 상속된다. 차단 Queues, 전송 Queues 및 Deques이다.

이 3개의 인터페이스는 대부분의 Java 사용 가능한 대기열에 의해 구현된다.

### 1) 차단 Queues
BlockingQueue 인터페이스는 스레드가 현재 상태에 따라 Queue에서 대기하도록 하는 추가 작업을 지원한다. 스레드는 검색을 시도할 때 Queue가 비어 있지 않거나 새 요소를 추가할 때 Queue가 비어 있을 때 까지 기다릴 수 있다.

표준 차단 Queues에는 LinkedBlockingQueue, SynchronousQueue 및 ArrayBlockingQueue가 포함된다.

### 2) 전송 Queues
TransferQueue 인터페이스는 BlockingQueue 인터페이스를 확장 하지만 생산자-소비자 패턴에 맞게 조정 된다. 생산자에서 소비자로의 정보 흐름을 제어하여 시스템에 역압을 생성한다.

Java는 TransferQueue 인터페이스의 구현인 LinkedTransferQueue와 함께 제공된다.

### 3) Deques
Deque는 **D**ouble-**E**nded **Que**ue의 줄임말 이며 카드 데크와 유사하다. 요소는 Deque의 시작과 끝 모두에서 가져올 수 있다. 전통적인 Queue와 마찬가지로 **Deque는 top과 bottom에 있는 요소를 추가, 검색 및 엿볼 수 있는 메서드를 제공한다.**

## 5. 우선순위 대기열
Java에서 만나는 대부분의 대기열이 FIFO 원칙을 따른다.

이 규칙에 대한 예외 중 하나는 PriorityQueue 이다. **새 요소가 우선 순위 대기열에 삽입되면 자연 순서 또는 우선순위 대기열을 구성할 때 제공된 정의된 비교기에 따라 순서가 지정된다.**

간단한 단위 테스트로 어떻게 작동하는지 확인한다.

```java
PriorityQueue<Integer> integerQueue = new PriorityQueue<>();

integerQueue.add(9);
integerQueue.add(2);
integerQueue.add(4);

int first = integerQueue.poll();
int second = integerQueue.poll();
int third = integerQueue.poll();

assertEquals(2, first);
assertEquals(4, second);
assertEquals(9, third);
```

정수가 Priority Queue에 추가된 순서에도 불구하고 **검색 순서가 숫자의 자연 순서에 따라 변경된 것을 볼 수 있다.**

Strings에 적용할 때도 마찬가지임을 알 수 있다.

```java
PriorityQueue<String> stringQueue = new PriorityQueue<>();

stringQueue.add("blueberry");
stringQueue.add("apple");
stringQueue.add("cherry");

String first = stringQueue.poll();
String second = stringQueue.poll();
String third = stringQueue.poll();

assertEquals("apple", first);
assertEquals("blueberry", second);
assertEquals("cherry", third);
```

## 6. 스레드 안전성
대기열에 항목을 추가하는 것은 다중 스레드 환경에서 특히 유용하다. **Queue는 스레드 간에 공유할 수 있으며 공간이 사용 가능할 때까지 진행을 차단하는 데 사용할 수 있다. 이는 일반적인 다중 스레드 문제를 해결하는 데 도움이 된다.**

예를 들어 여러 스레드에서 단일 디스크에 쓰면 리소스 경합이 발생하여 쓰기 시간이 느려질 수 있다. **BlockingQueue를 사용하여 단일 작성자 스레드를 생성하면 이 문제를 완화하고 쓰기 속도를 크게 향상시킬 수 있다.**

다행스럽게도 Java는 스레드로부터 안전하고 다중 스레드 프로그램에 완벽한 ConcurrentLinkedQueue, ArrayBlockingQueue 및 ConcurrentLinkedDeque를 제공한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-queue](https://www.baeldung.com/java-queue)
