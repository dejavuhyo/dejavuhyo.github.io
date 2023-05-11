---
title: Java Concurrent Queues
author: dejavuhyo
date: 2023-05-12 08:45:00 +0900
categories: [Language, Java]
tags: [java-queues, concurrent-queues, 자바-큐, 큐]
---

## 1. Blocking vs Non-Blocking 대기열
다중 스레드 애플리케이션에서 대기열은 여러 동시 생산자-소비자 시나리오를 처리해야 한다. 동시 대기열의 올바른 선택은 알고리즘에서 우수한 성능을 달성하는 데 중요할 수 있다.

## 2. Blocking vs Non-Blocking 대기열
**BlockingQueue는 간단한 스레드 안전 메커니즘을 제공한다.** 이 큐에서 스레드는 큐의 가용성을 기다려야 한다. 생산자는 요소를 추가하기 전에 사용 가능한 용량을 기다리고, 소비자는 대기열이 비워질 때까지 기다린다. 이러한 경우 비차단 대기열은 예외를 발생시키거나 null 또는 false와 같은 특수 값을 반환한다.

이 차단 메커니즘을 달성하기 위해 BlockingQueue 인터페이스는 일반 Queue 함수 위에 두 가지 함수인 put 및 take를 노출한다. 이러한 기능은 표준 Queue의 추가 및 제거와 동일하다.

## 3. 동시 대기열 구현

### 1) ArrayBlockingQueue
이름에서 알 수 있듯이 이 대기열은 내부적으로 배열을 사용한다. 결과적으로 제한된 큐이며 크기가 고정되어 있음을 의미힌다.

간단한 작업 대기열은 사용 사례의 예이다. 이 시나리오는 생산자 대 소비자 비율이 낮은 경우가 많으며 시간이 많이 걸리는 작업을 여러 작업자에게 분할한다. 이 대기열은 무한정 커질 수 없으므로 크기 제한은 메모리가 문제인 경우 안전 임계값 역할을 한다.

메모리에 대해 말하면, 대기열이 배열을 미리 할당한다는 점에 유의해야 한다. 이렇게 하면 처리량이 향상될 수 있지만 필요한 것보다 더 많은 메모리를 소비할 수도 있다. 예를 들어 대용량 대기열은 오랫동안 비어 있을 수 있다.

또한 ArrayBlockingQueue는 넣기 및 가져오기 작업 모두에 단일 잠금을 사용한다. 이렇게 하면 성능이 저하되는 대신 항목을 덮어쓰지 않는다.

### 2) LinkedBlockingQueue
LinkedBlockingQueue는 각 대기열 항목이 새 노드인 LinkedList 변형을 사용한다. 이렇게 하면 원칙적으로 대기열이 제한되지 않지만 여전히 `Integer.MAX_VALUE`의 엄격한 제한이 있다.

반면 `LinkedBlockingQueue(int capacity)` 생성자를 사용하여 대기열 크기를 설정할 수 있다.

이 대기열은 넣기 및 가져오기 작업에 고유한 잠금을 사용한다. 결과적으로 두 작업을 병렬로 수행하고 처리량을 향상시킬 수 있다.

LinkedBlockingQueue는 제한되거나 제한되지 않을 수 있으므로 ArrayBlockingQueue를 사용하는 이유는, LinkedBlockingQueue는 대기열에서 항목이 추가되거나 제거될 때마다 노드를 할당 및 할당 해제해야 한다. 이러한 이유로 대기열이 빠르게 증가하고 빠르게 축소되는 경우 ArrayBlockingQueue가 더 나은 대안이 될 수 있다.

LinkedBlockingQueue의 성능은 예측할 수 없다고 한다. 즉, 올바른 데이터 구조를 사용하도록 항상 시나리오를 프로파일링해야 힌다.

### 3) PriorityBlockingQueue
PriorityBlockingQueue는 특정 순서로 항목을 소비해야 할 때 사용하는 솔루션이다. 이를 달성하기 위해 PriorityBlockingQueue는 어레이 기반 바이너리 힙을 사용한다.

내부적으로는 단일 잠금 메커니즘을 사용하지만 가져오기 작업은 넣기 작업과 동시에 발생할 수 있다. 간단한 스핀록을 사용하면 가능하다.

일반적인 사용 사례는 우선 순위가 다른 작업을 사용하는 것이다. 우선 순위가 낮은 작업이 우선 순위가 높은 작업을 대신하는 것을 원하지 않는다.

### 4) DelayQueue
소비자가 만료된 항목만 가져갈 수 있는 경우 DelayQueue를 사용한다. 내부적으로 PriorityQueue를 사용하여 만료일까지 항목을 주문한다.

이것은 범용 대기열이 아니기 때문에 ArrayBlockingQueue 또는 LinkedBlockingQueue 만큼 많은 시나리오를 다루지 않는다. 예를 들어 이 대기열을 사용하여 NodeJS에서 볼 수 있는 것과 유사한 간단한 이벤트 루프를 구현할 수 있다. 비동기 작업이 만료되면 나중에 처리할 수 있도록 대기열에 넣는다.

### 5) LinkedTransferQueue
LinkedTransferQueue는 전송 방법을 도입한다. 다른 대기열은 일반적으로 항목을 생성하거나 소비할 때 차단되지만 LinkedTransferQueue를 사용하면 생산자가 항목 소비를 기다릴 수 있다.

대기열에 넣은 특정 항목을 누군가가 가져갔다는 보장이 필요할 때 LinkedTransferQueue를 사용한다. 또한 이 대기열을 사용하여 간단한 배압 알고리즘을 구현할 수 있다. 실제로 소비될 때까지 생산자를 차단함으로써 소비자는 생성된 메시지의 흐름을 주도할 수 있다.

### 6) SynchronousQueue
대기열에는 일반적으로 많은 항목이 포함되지만 SynchronousQueue에는 항상 최대 1개의 항목만 포함된다. 즉, SynchronousQueue를 두 스레드 간에 일부 데이터를 교환하는 간단한 방법으로 볼 필요가 있다.

공유 상태에 액세스해야 하는 두 개의 스레드가 있는 경우 종종 이를 CountDownLatch 또는 다른 동기화 메커니즘과 동기화한다. SynchronousQueue를 사용하면 스레드의 수동 동기화를 피할 수 있다.

### 7) ConcurrentLinkedQueue
ConcurrentLinkedQueue는 유일한 비차단 대기열이다. 결과적으로 `add` 및 `poll`이 스레드로부터 안전하고 즉시 반환되도록 보장되는 "대기 없는" 알고리즘을 제공한다. 잠금 대신 이 큐는 CAS(Compare-And-Swap)를 사용한다.

내부적으로는 `Maged M. Michael`과 `Michael L. Scott.`의 [Simple, Fast, and Practical Non-Blocking and Blocking Concurrent Queue Algorithms](https://www.cs.rochester.edu/u/scott/papers/1996_PODC_queues.pdf)의 알고리즘을 기반으로 한다.

블로킹 데이터 구조를 사용하는 것이 종종 금지되는 최신 반응 시스템에 대한 완벽한 후보 이다.

반면에 소비자가 루프에서 대기하는 경우 더 나은 대안으로 차단 대기열을 선택해야 한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-concurrent-queues](https://www.baeldung.com/java-concurrent-queues)
