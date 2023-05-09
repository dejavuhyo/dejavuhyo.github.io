---
title: Java DelayQueue
author: dejavuhyo
date: 2023-05-09 09:40:00 +0900
categories: [Language, Java]
tags: [java-queue, delay-queue, 자바-큐, 큐]
---

## 1. DelayQueue의 요소에 대해 지연 구현
DelayQueue에 넣고자 하는 각 요소는 Delayed 인터페이스를 구현해야 한다. DelayObject 클래스를 생성하려고 하면, 해당 클래스의 인스턴스는 DelayQueue에 저장된다.

문자열 데이터와 delayInMilliseconds를 생성자에 인수로 전달한다.

```java
public class DelayObject implements Delayed {
    private String data;
    private long startTime;

    public DelayObject(String data, long delayInMilliseconds) {
        this.data = data;
        this.startTime = System.currentTimeMillis() + delayInMilliseconds;
    }
}
```

startTime을 정의하고 있다. 이것은 대기열에서 요소를 소비해야 하는 시간이다. 다음으로 `getDelay()` 메서드를 구현해야 한다. 이 메서드는 주어진 시간 단위로 이 개체와 관련된 나머지 지연을 반환해야 한다.

따라서 적절한 TimeUnit에서 남은 지연을 반환하려면 `TimeUnit.convert()` 메서드를 사용해야 한다.

```java
@Override
public long getDelay(TimeUnit unit) {
    long diff = startTime - System.currentTimeMillis();
    return unit.convert(diff, TimeUnit.MILLISECONDS);
}
```

소비자가 대기열에서 요소를 가져오려고 하면 DelayQueue는 `getDelay()`를 실행하여 해당 요소가 대기열에서 반환될 수 있는지 확인한다. `getDelay()` 메서드가 0 또는 음수를 반환하면 대기열에서 검색할 수 있음을 의미한다.

DelayQueue의 요소가 만료 시간에 따라 정렬되기 때문에 `compareTo()` 메서드도 구현해야 한다. 먼저 만료되는 항목은 큐의 맨 위에 보관되고 만료 시간이 가장 긴 요소는 큐의 맨 뒤에 보관된다.

```java
@Override
public int compareTo(Delayed o) {
    return Ints.saturatedCast(
      this.startTime - ((DelayObject) o).startTime);
}
```

## 2. DelayQueue 소비자 및 생산자
DelayQueue를 테스트하려면 생산자 및 소비자 로직을 구현해야 한다. 생산자 클래스는 대기열, 생산할 요소 수, 밀리초 단위의 각 메시지 지연을 인수로 사용한다.

그런 다음 `run()` 메서드가 호출되면 요소를 대기열에 넣고 각 입력 후 500밀리초 동안 휴면한다.

```java
public class DelayQueueProducer implements Runnable {
 
    private BlockingQueue<DelayObject> queue;
    private Integer numberOfElementsToProduce;
    private Integer delayOfEachProducedMessageMilliseconds;

    // standard constructor

    @Override
    public void run() {
        for (int i = 0; i < numberOfElementsToProduce; i++) {
            DelayObject object
              = new DelayObject(
                UUID.randomUUID().toString(), delayOfEachProducedMessageMilliseconds);
            System.out.println("Put object: " + object);
            try {
                queue.put(object);
                Thread.sleep(500);
            } catch (InterruptedException ie) {
                ie.printStackTrace();
            }
        }
    }
}
```

소비자 구현은 매우 유사하지만 소비된 메시지 수를 추적하기도 한다.

```java
public class DelayQueueConsumer implements Runnable {
    private BlockingQueue<DelayObject> queue;
    private Integer numberOfElementsToTake;
    public AtomicInteger numberOfConsumedElements = new AtomicInteger();

    // standard constructors

    @Override
    public void run() {
        for (int i = 0; i < numberOfElementsToTake; i++) {
            try {
                DelayObject object = queue.take();
                numberOfConsumedElements.incrementAndGet();
                System.out.println("Consumer take: " + object);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 3. DelayQueue 사용 테스트
DelayQueue의 동작을 테스트하기 위해 하나의 생산자 스레드와 하나의 소비자 스레드를 만든다.

생산자는 500밀리초 지연으로 두 개체를 대기열에 넣는다. 테스트는 소비자가 두 개의 메시지를 소비했다고 주장한다.

```java
@Test
public void givenDelayQueue_whenProduceElement
  _thenShouldConsumeAfterGivenDelay() throws InterruptedException {
    // given
    ExecutorService executor = Executors.newFixedThreadPool(2);
    
    BlockingQueue<DelayObject> queue = new DelayQueue<>();
    int numberOfElementsToProduce = 2;
    int delayOfEachProducedMessageMilliseconds = 500;
    DelayQueueConsumer consumer = new DelayQueueConsumer(
      queue, numberOfElementsToProduce);
    DelayQueueProducer producer = new DelayQueueProducer(
      queue, numberOfElementsToProduce, delayOfEachProducedMessageMilliseconds);

    // when
    executor.submit(producer);
    executor.submit(consumer);

    // then
    executor.awaitTermination(5, TimeUnit.SECONDS);
    executor.shutdown();
 
    assertEquals(consumer.numberOfConsumedElements.get(), numberOfElementsToProduce);
}
```

이 프로그램을 실행하면 다음과 같은 결과가 나온다.

```text
Put object: {data='86046157-e8a0-49b2-9cbb-8326124bcab8', startTime=1494069868007}
Consumer take: {data='86046157-e8a0-49b2-9cbb-8326124bcab8', startTime=1494069868007}
Put object: {data='d47927ef-18c7-449b-b491-5ff30e6795ed', startTime=1494069868512}
Consumer take: {data='d47927ef-18c7-449b-b491-5ff30e6795ed', startTime=1494069868512}
```

생산자는 개체를 넣고 잠시 후 지연이 만료된 첫 번째 개체가 소비된다.

두 번째 요소에 대해서도 동일한 상황이 발생했다.

## 4. 주어진 시간 내에 소비할 수 없는 소비자
10초 안에 만료되는 요소를 생산하는 생산자가 있다고 가정한다.

```java
int numberOfElementsToProduce = 1;
int delayOfEachProducedMessageMilliseconds = 10_000;
DelayQueueConsumer consumer = new DelayQueueConsumer(
  queue, numberOfElementsToProduce);
DelayQueueProducer producer = new DelayQueueProducer(
  queue, numberOfElementsToProduce, delayOfEachProducedMessageMilliseconds);
```

테스트를 시작하지만 5초 후에 종료된다. DelayQueue의 특성으로 인해 소비자는 요소가 아직 만료되지 않았기 때문에 대기열에서 메시지를 사용할 수 없다.

```java
executor.submit(producer);
executor.submit(consumer);

executor.awaitTermination(5, TimeUnit.SECONDS);
executor.shutdown();
assertEquals(consumer.numberOfConsumedElements.get(), 0);
```

소비자의 numberOfConsumedElements 값은 0이다.

## 5. 즉시 만료되는 요소 생성
지연 메시지 `getDelay()` 메서드의 구현이 음수를 반환하면 해당 요소가 이미 만료되었음을 의미한다. 이 상황에서 생산자는 해당 요소를 즉시 소비한다.

음수 지연으로 요소를 생성하는 상황을 테스트할 수 있다.

```java
int numberOfElementsToProduce = 1;
int delayOfEachProducedMessageMilliseconds = -10_000;
DelayQueueConsumer consumer = new DelayQueueConsumer(queue, numberOfElementsToProduce);
DelayQueueProducer producer = new DelayQueueProducer(
  queue, numberOfElementsToProduce, delayOfEachProducedMessageMilliseconds);
```

테스트 케이스를 시작하면 소비자는 이미 만료된 요소를 즉시 소비한다.

```java
executor.submit(producer);
executor.submit(consumer);

executor.awaitTermination(1, TimeUnit.SECONDS);
executor.shutdown();
assertEquals(consumer.numberOfConsumedElements.get(), 1);
```

## [출처 및 참고]
* [https://www.baeldung.com/java-delay-queue](https://www.baeldung.com/java-delay-queue)
