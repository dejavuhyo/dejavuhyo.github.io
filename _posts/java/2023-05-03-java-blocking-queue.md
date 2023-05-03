---
title: Java BlockingQueue
author: dejavuhyo
date: 2023-05-03 09:30:00 +0900
categories: [Language, Java]
tags: [java-queue, blocking-queue, 자바-블로킹-큐, 블로킹-큐, 자바-큐, 큐]
---

## 1. BlockingQueue 유형
두 가지 유형의 BlockingQueue를 구분할 수 있다.

* 무제한 대기열 - 거의 무한정 커질 수 있음

* 제한된 대기열 - 최대 용량이 정의됨

### 1) 무제한 대기열
무제한 대기열을 만드는 것은 간단하다.

```java
BlockingQueue<String> blockingQueue = new LinkedBlockingDeque<>();
```

blockingQueue의 용량은 `Integer.MAX_VALUE`로 설정된다. 제한되지 않은 대기열에 요소를 추가하는 모든 작업은 절대 차단되지 않으므로 매우 큰 크기로 커질 수 있다.

무제한 BlockingQueue를 사용하여 생산자-소비자 프로그램을 설계할 때 가장 중요한 것은 생산자가 대기열에 메시지를 추가하는 것처럼 소비자가 메시지를 빠르게 소비할 수 있어야 한다는 것이다. 그렇지 않으면 메모리가 가득 차서 OutOfMemory 예외가 발생한다.

### 2) 제한된 대기열
두 번째 유형의 대기열은 제한된 대기열이다. 용량을 생성자에 대한 인수로 전달하여 이러한 대기열을 만들 수 있다.

```java
BlockingQueue<String> blockingQueue = new LinkedBlockingDeque<>(10);
```

용량이 10인 blockingQueue가 있다. 즉, 생산자가 요소를 추가하는 데 사용된 메서드(`offer()`, `add()` 또는 `put()`)에 따라 이미 가득 찬 대기열에 요소를 추가하려고 할 때, 객체를 삽입할 수 있는 공간이 생길 때까지 차단된다. 그렇지 않으면 작업이 실패한다.

제한된 대기열을 사용하는 것은 동시 프로그램을 설계하는 좋은 방법이다. 왜냐하면 이미 가득 찬 대기열에 요소를 삽입할 때 해당 작업은 소비자가 따라잡아 대기열에서 사용 가능한 공간을 만들 때까지 기다려야 하기 때문이다. 그것은 우리의 노력 없이도 스로틀링을 제공한다.

## 2. BlockingQueue API
BlockingQueue 인터페이스에는 두 가지 유형의 메소드가 있다. 큐에 요소를 추가하는 메소드와 해당 요소를 검색하는 메소드이다. 이 두 그룹의 각 메서드는 대기열이 가득 차거나 비어 있는 경우 다르게 동작한다.

### 1) 요소 추가

* `add()` - 삽입에 성공하면 true를 반환하고, 그렇지 않으면 IllegalStateException을 발생시킨다.

* `put()` - 지정된 요소를 대기열에 삽입하고 필요한 경우 여유 슬롯을 기다린다.

* `offer()` - 삽입에 성공하면 true를 반환하고 그렇지 않으면 false를 반환한다.

* `offer(E e, long timeout, TimeUnit unit)` - 요소를 대기열에 삽입하려고 시도하고 지정된 시간 제한 내에서 사용 가능한 슬롯을 기다립니다.

### 2) 요소 검색

* `take()` - 큐의 헤드 요소를 기다렸다가 제거한다. 큐가 비어 있으면 차단하고 요소를 사용할 수 있을 때까지 기다린다.

* `poll(long timeout, TimeUnit unit)` - 대기열의 헤드를 검색하고 제거하며 필요한 경우 요소를 사용할 수 있도록 지정된 대기 시간까지 기다린다. 시간 초과 후 null을 반환한다.

이러한 메서드는 생산자-소비자 프로그램을 구축할 때 BlockingQueue 인터페이스에서 가장 중요한 빌딩 블록이다.

## 3. 다중 스레드 생산자-소비자 예
생산자와 소비자라는 두 부분으로 구성된 프로그램을 만든다.

생산자는 0에서 100까지의 임의의 숫자를 생성하고 해당 숫자를 BlockingQueue에 넣는다. 4개의 생산자 스레드를 가지고 큐에 사용 가능한 공간이 있을 때까지 차단하기 위해 `put()` 메서드를 사용할 것이다.

중요한 점은 소비자 스레드가 대기열에 요소가 무한정 나타날 때까지 기다리지 않도록 해야 한다는 것이다.

처리할 메시지가 더 이상 없다는 것을 생산자에서 소비자에게 알리는 좋은 기술은 독약이라는 특별한 메시지를 보내는 것이다. 소비자가 있는 만큼 많은 독약을 보내야 한다. 그런 다음 소비자가 대기열에서 특별한 독약 메시지를 가져오면 정상적으로 실행이 완료된다.

생산자 클래스를 살펴본다.

```java
public class NumbersProducer implements Runnable {
    private BlockingQueue<Integer> numbersQueue;
    private final int poisonPill;
    private final int poisonPillPerProducer;
    
    public NumbersProducer(BlockingQueue<Integer> numbersQueue, int poisonPill, int poisonPillPerProducer) {
        this.numbersQueue = numbersQueue;
        this.poisonPill = poisonPill;
        this.poisonPillPerProducer = poisonPillPerProducer;
    }
    public void run() {
        try {
            generateNumbers();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
    
    private void generateNumbers() throws InterruptedException {
        for (int i = 0; i < 100; i++) {
            numbersQueue.put(ThreadLocalRandom.current().nextInt(100));
        }
        for (int j = 0; j < poisonPillPerProducer; j++) {
            numbersQueue.put(poisonPill);
        }
     }
}
```

생산자 생성자는 생산자와 소비자 간의 처리를 조정하는 데 사용되는 BlockingQueue를 인수로 사용한다. `generateNumbers()` 메서드가 100개의 요소를 대기열에 넣는 것을 볼 수 있다. 또한 실행이 완료될 때 어떤 유형의 메시지를 대기열에 넣어야 하는지 알기 위해 독약 메시지가 필요하다. 해당 메시지는 poisonPillPerProducer 시간을 대기열에 넣어야 한다.

각 소비자는 `take()` 메서드를 사용하여 BlockingQueue에서 요소를 가져오므로 대기열에 요소가 있을 때까지 차단된다. 대기열에서 Integer를 가져온 후 메시지가 독약인지 확인하고, 그렇다면 스레드 실행이 완료된 것이다. 그렇지 않으면 현재 스레드 이름과 함께 표준 출력에 결과를 인쇄한다.

이를 통해 소비자의 내부 작업에 대한 통찰력을 얻을 수 있다.

```java
public class NumbersConsumer implements Runnable {
    private BlockingQueue<Integer> queue;
    private final int poisonPill;
    
    public NumbersConsumer(BlockingQueue<Integer> queue, int poisonPill) {
        this.queue = queue;
        this.poisonPill = poisonPill;
    }
    public void run() {
        try {
            while (true) {
                Integer number = queue.take();
                if (number.equals(poisonPill)) {
                    return;
                }
                System.out.println(Thread.currentThread().getName() + " result: " + number);
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

중요한 사항은 대기열의 사용이다. 생산자 생성자와 동일하게 대기열이 인수로 전달된다. BlockingQueue는 명시적인 동기화 없이도 스레드 간에 공유할 수 있기 때문에 가능하다.

이제 생산자와 소비자가 있으므로 프로그램을 시작할 수 있다. 대기열의 용량을 정의해야 하며 100개의 요소로 설정한다.

4개의 생산자 스레드를 원하고 소비자 스레드의 수는 사용 가능한 프로세서의 수와 같다.

```java
int BOUND = 10;
int N_PRODUCERS = 4;
int N_CONSUMERS = Runtime.getRuntime().availableProcessors();
int poisonPill = Integer.MAX_VALUE;
int poisonPillPerProducer = N_CONSUMERS / N_PRODUCERS;
int mod = N_CONSUMERS % N_PRODUCERS;

BlockingQueue<Integer> queue = new LinkedBlockingQueue<>(BOUND);

for (int i = 1; i < N_PRODUCERS; i++) {
    new Thread(new NumbersProducer(queue, poisonPill, poisonPillPerProducer)).start();
}

for (int j = 0; j < N_CONSUMERS; j++) {
    new Thread(new NumbersConsumer(queue, poisonPill)).start();
}

new Thread(new NumbersProducer(queue, poisonPill, poisonPillPerProducer + mod)).start();
```

BlockingQueue는 용량이 있는 구성을 사용하여 생성된다. 4명의 생산자와 N명의 소비자를 만들고 있다. 정상적인 작업 조건에서는 생산자가 이러한 값을 보내지 않기 때문에 독약 메시지를 `Integer.MAX_VALUE`로 지정한다. 여기서 가장 중요한 점은 BlockingQueue가 이들 간의 작업을 조정하는 데 사용된다는 것이다.

프로그램을 실행하면 4개의 생산자 스레드가 임의의 정수를 BlockingQueue에 넣고 소비자는 대기열에서 해당 요소를 가져온다. 각 스레드는 결과와 함께 스레드의 이름을 표준 출력으로 출력한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-blocking-queue](https://www.baeldung.com/java-blocking-queue)
