---
title: Java SynchronousQueue
author: dejavuhyo
date: 2023-05-04 08:25:00 +0900
categories: [Language, Java]
tags: [java-queue, synchronous-queue, 자바-큐, 큐]
---

## 1. API 개요
SynchronousQueue에는 `take()` 및 `put()` 이라는 두 가지 작업만 지원되며 둘 다 차단된다.

예를 들어 큐에 요소를 추가하려면 `put()` 메서드를 호출해야 한다. 해당 메서드는 다른 스레드가 요소를 가져올 준비가 되었음을 알리는 `take()` 메서드를 호출할 때까지 차단된다.

SynchronousQueue에는 대기열의 인터페이스가 있지만 한 스레드가 요소를 전달하고 다른 스레드가 해당 요소를 가져오는 두 스레드 사이의 단일 요소에 대한 교환 지점으로 생각해야 한다.

## 2. 공유 변수를 사용하여 핸드오프 구현
SynchronousQueue가 유용한 이유를 알아보기 위해 두 스레드 간에 공유 변수를 사용하여 논리를 구현하고 다음으로 SynchronousQueue를 사용하여 해당 논리를 다시 작성하여 코드를 훨씬 더 간단하고 읽기 쉽게 만든다.

생산자와 소비자라는 두 개의 스레드가 있고 생산자가 공유 변수의 값을 설정할 때, 소비자 스레드에 그 사실을 알리고 싶다. 다음으로 소비자 스레드는 공유 변수에서 값을 가져온다.

소비자가 아직 설정되지 않은 공유 변수의 값에 액세스하는 상황을 방지하기 위해 CountDownLatch를 사용하여 이 두 스레드를 조정한다.

조정 처리에 사용할 SharedState 변수와 CountDownLatch를 정의한다.

```javaExecutorService executor = Executors.newFixedThreadPool(2);
AtomicInteger sharedState = new AtomicInteger();
CountDownLatch countDownLatch = new CountDownLatch(1);
```

생산자는 임의의 정수를 sharedState 변수에 저장하고 countDownLatch에서 `countDown()` 메서드를 실행하여 SharedState에서 값을 가져올 수 있음을 소비자에게 알린다.

```java
Runnable producer = () -> {
    Integer producedElement = ThreadLocalRandom
      .current()
      .nextInt();
    sharedState.set(producedElement);
    countDownLatch.countDown();
};
```

소비자는 `await()` 메서드를 사용하여 countDownLatch에서 대기한다. 생산자가 변수가 설정되었다는 신호를 보내면 소비자는 sharedState에서 변수를 가져온다.

```java
Runnable consumer = () -> {
    try {
        countDownLatch.await();
        Integer consumedElement = sharedState.get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    }
};
```

마지막으로 프로그램을 시작한다.

```java
executor.execute(producer);
executor.execute(consumer);

executor.awaitTermination(500, TimeUnit.MILLISECONDS);
executor.shutdown();
assertEquals(countDownLatch.getCount(), 0);
```

다음과 같이 출력된다.

```text
Saving an element: -1507375353 to the exchange point
consumed an element: -1507375353 from the exchange point
```

두 스레드 간에 요소를 교환하는 것과 같은 간단한 기능을 구현하는 데 많은 코드가 있음을 알 수 있다.

## 3. SynchronousQueue를 사용하여 핸드오프 구현
이전과 동일한 기능을 SynchronousQueue를 사용하여 구현한다. SynchronousQueue 외에 다른 것을 사용할 필요가 없도록 스레드 간에 상태를 교환하고 해당 작업을 조정하는 데 사용할 수 있기 때문에 이중 효과가 있다.

먼저 대기열을 정의한다.

```java
ExecutorService executor = Executors.newFixedThreadPool(2);
SynchronousQueue<Integer> queue = new SynchronousQueue<>();
```

생산자는 다른 스레드가 대기열에서 요소를 가져올 때까지 차단되는 `put()` 메서드를 호출한다.

```java
Runnable producer = () -> {
    Integer producedElement = ThreadLocalRandom
      .current()
      .nextInt();
    try {
        queue.put(producedElement);
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    }
};
```

소비자는 단순히 `take()` 메서드를 사용하여 해당 요소를 검색한다.

```java
Runnable consumer = () -> {
    try {
        Integer consumedElement = queue.take();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    }
};
```

다음으로 프로그램을 시작한다.

```java
executor.execute(producer);
executor.execute(consumer);

executor.awaitTermination(500, TimeUnit.MILLISECONDS);
executor.shutdown();
assertEquals(queue.size(), 0);
```

다음과 같이 출력된다.

```text
Saving an element: 339626897 to the exchange point
consumed an element: 339626897 from the exchange point
```

SynchronousQueue가 스레드 간의 교환 지점으로 사용되는 것을 볼 수 있다. 이는 CountDownLatch와 함께 공유 상태를 사용한 이전 예제보다 훨씬 더 좋고 이해하기 쉽다.

## [출처 및 참고]
* [https://www.baeldung.com/java-synchronous-queue](https://www.baeldung.com/java-synchronous-queue)
