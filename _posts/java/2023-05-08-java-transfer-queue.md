---
title: Java TransferQueue
author: dejavuhyo
date: 2023-05-08 10:25:00 +0900
categories: [Language, Java]
tags: [java-queue, transfer-queue, java-queue, 자바-큐, 큐]
---

## 1. One 생산자 - Zero 소비자
TransferQueue에서 `transfer()` 메서드를 테스트한다. 예상되는 동작은 소비자가 `take()` 메서드를 사용하여 대기열에서 메시지를 받을 때까지 생산자가 차단된다는 것이다.

이를 달성하기 위해 생산자는 하나지만 소비자는 없는 프로그램을 만든다. 대기열에서 해당 요소를 가져올 소비자가 없기 때문에 생산자 스레드의 첫 번째 `transfer()` 호출은 무기한 차단된다.

Producer 클래스이다.

```java
class Producer implements Runnable {
    private TransferQueue<String> transferQueue;

    private String name;

    private Integer numberOfMessagesToProduce;

    public AtomicInteger numberOfProducedMessages
      = new AtomicInteger();

    @Override
    public void run() {
        for (int i = 0; i < numberOfMessagesToProduce; i++) {
            try {
                boolean added 
                  = transferQueue.tryTransfer("A" + i, 4000, TimeUnit.MILLISECONDS);
                if(added){
                    numberOfProducedMessages.incrementAndGet();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    // standard constructors
}
```

TransferQueue의 인스턴스를 생산자에 지정하려는 이름과 대기열로 전송해야 하는 요소 수와 함께 생성자에 전달하고 있다.

주어진 시간 초과로 `tryTransfer()` 메서드를 사용하고 있다. 4초를 기다리고 있고, 생산자가 주어진 제한 시간 내에 메시지를 전송할 수 없으면 false를 반환 하고 다음 메시지로 넘어간다. 생성자에는 생성된 메시지 수를 추적하기 위한 numberOfProducedMessages 변수가 있다.

다음으로 Consumer 클래스이다.

```java
class Consumer implements Runnable {

    private TransferQueue<String> transferQueue;

    private String name;

    private int numberOfMessagesToConsume;

    public AtomicInteger numberOfConsumedMessages
     = new AtomicInteger();

    @Override
    public void run() {
        for (int i = 0; i < numberOfMessagesToConsume; i++) {
            try {
                String element = transferQueue.take();
                longProcessing(element);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    private void longProcessing(String element)
      throws InterruptedException {
        numberOfConsumedMessages.incrementAndGet();
        Thread.sleep(500);
    }
    
    // standard constructors
}
```

생산자와 유사하지만 `take()` 메소드를 사용하여 큐에서 요소를 수신한다. 또한 수신된 메시지의 카운터인 numberOfConsumedMessages 변수를 증가시키는 `longProcessing()` 메서드를 사용하여 장기 실행 작업을 시뮬레이션하고 있다.

이제 단 한 명의 프로듀서로 프로그램을 시작한다.

```java
@Test
public void whenUseOneProducerAndNoConsumers_thenShouldFailWithTimeout() 
  throws InterruptedException {
    // given
    TransferQueue<String> transferQueue = new LinkedTransferQueue<>();
    ExecutorService exService = Executors.newFixedThreadPool(2);
    Producer producer = new Producer(transferQueue, "1", 3);

    // when
    exService.execute(producer);

    // then
    exService.awaitTermination(5000, TimeUnit.MILLISECONDS);
    exService.shutdown();

    assertEquals(producer.numberOfProducedMessages.intValue(), 0);
}
```

세 개의 요소를 대기열에 보내고 싶지만 생산자는 첫 번째 요소에서 차단되고 대기열에서 해당 요소를 가져올 소비자가 없다. 메시지가 소비되거나 제한 시간에 도달할 때까지 차단하는 `tryTransfer()` 메서드를 사용하고 있다. 시간 초과 후 전송이 실패했음을 나타내기 위해 false를 반환 하고 다음 전송을 시도한다.

예제의 출력이다.

```text
Producer: 1 is waiting to transfer...
can not add an element due to the timeout
Producer: 1 is waiting to transfer...
```

## 2. One 생산자 - One 소비자
하나의 생산자와 하나의 소비자가 있는 상황을 테스트 한다.

```java
@Test
public void whenUseOneConsumerAndOneProducer_thenShouldProcessAllMessages() 
  throws InterruptedException {
    // given
    TransferQueue<String> transferQueue = new LinkedTransferQueue<>();
    ExecutorService exService = Executors.newFixedThreadPool(2);
    Producer producer = new Producer(transferQueue, "1", 3);
    Consumer consumer = new Consumer(transferQueue, "1", 3);

    // when
    exService.execute(producer);
    exService.execute(consumer);

    // then
    exService.awaitTermination(5000, TimeUnit.MILLISECONDS);
    exService.shutdown();

    assertEquals(producer.numberOfProducedMessages.intValue(), 3);
    assertEquals(consumer.numberOfConsumedMessages.intValue(), 3);
}
```

TransferQueue는 교환 지점으로 사용되며 소비자가 대기열의 요소를 소비할 때까지 생산자는 다른 요소를 추가할 수 없다. 프로그램 출력이다.

```text
Producer: 1 is waiting to transfer...
Consumer: 1 is waiting to take element...
Producer: 1 transferred element: A0
Producer: 1 is waiting to transfer...
Consumer: 1 received element: A0
Consumer: 1 is waiting to take element...
Producer: 1 transferred element: A1
Producer: 1 is waiting to transfer...
Consumer: 1 received element: A1
Consumer: 1 is waiting to take element...
Producer: 1 transferred element: A2
Consumer: 1 received element: A2
```

TransferQueue의 사양으로 인해 대기열에서 요소를 생산하고 소비하는 것이 순차적이라는 것을 알 수 있다.

## 3. Many 생산자 - Many 소비자
마지막 예는 여러 소비자와 여러 생산자이다.

```java
@Test
public void whenMultipleConsumersAndProducers_thenProcessAllMessages() 
  throws InterruptedException {
    // given
    TransferQueue<String> transferQueue = new LinkedTransferQueue<>();
    ExecutorService exService = Executors.newFixedThreadPool(3);
    Producer producer1 = new Producer(transferQueue, "1", 3);
    Producer producer2 = new Producer(transferQueue, "2", 3);
    Consumer consumer1 = new Consumer(transferQueue, "1", 3);
    Consumer consumer2 = new Consumer(transferQueue, "2", 3);

    // when
    exService.execute(producer1);
    exService.execute(producer2);
    exService.execute(consumer1);
    exService.execute(consumer2);

    // then
    exService.awaitTermination(10_000, TimeUnit.MILLISECONDS);
    exService.shutdown();

    assertEquals(producer1.numberOfProducedMessages.intValue(), 3);
    assertEquals(producer2.numberOfProducedMessages.intValue(), 3);
}
```

이 예에서는 두 명의 소비자와 두 명의 생산자가 있다. 프로그램이 시작되면 두 생산자가 하나의 요소를 생산할 수 있고 그 후에는 소비자 중 하나가 대기열에서 해당 요소를 가져올 때까지 차단된다.

```text
Producer: 1 is waiting to transfer...
Consumer: 1 is waiting to take element...
Producer: 2 is waiting to transfer...
Producer: 1 transferred element: A0
Producer: 1 is waiting to transfer...
Consumer: 1 received element: A0
Consumer: 1 is waiting to take element...
Producer: 2 transferred element: A0
Producer: 2 is waiting to transfer...
Consumer: 1 received element: A0
Consumer: 1 is waiting to take element...
Producer: 1 transferred element: A1
Producer: 1 is waiting to transfer...
Consumer: 1 received element: A1
Consumer: 2 is waiting to take element...
Producer: 2 transferred element: A1
Producer: 2 is waiting to transfer...
Consumer: 2 received element: A1
Consumer: 2 is waiting to take element...
Producer: 1 transferred element: A2
Consumer: 2 received element: A2
Consumer: 2 is waiting to take element...
Producer: 2 transferred element: A2
Consumer: 2 received element: A2
```

## [출처 및 참고]
* [https://www.baeldung.com/java-transfer-queue](https://www.baeldung.com/java-transfer-queue)
