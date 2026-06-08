---
title: Java Thread.onSpinWait()
author: dejavuhyo
date: 2026-06-08 11:00:00 +0900
categories: [Language, Java]
tags: [java-thread-onspinwait, java-thread, thread-onspinwait, onspinwait]
---

![openjdk-logo](/assets/img/2026-06-08-java-thread-onspinwait/openjdk-logo.png)

## 1. Thread.onSpinWait()란
Thread.onSpinWait()는 Java 9에서 도입된 기능으로, 현재 스레드가 스핀 루프(Spin Loop, 또는 Busy-Waiting) 상태에 있다는 것을 JVM과 하드웨어(CPU)에 알려주는 일종의 힌트(Hint)이다.

## 2. 스핀 루프(Spin Loop)
스핀 루프는 특정 조건이 만족될 때까지 스레드가 잠들지(Block) 않고 무한 반복문(while)을 돌면서 기다리는 방식이다.

* 스핀 루프 예시

```java
volatile boolean isReady = false;

while (!isReady) {
    // 아무것도 하지 않고 isReady가 true가 될 때까지 무한 반복
}
```

* 장점: 스레드가 잠들었다가 깨어나는 과정(Context Switching)이 없기 때문에, 대기 시간이 아주 짧을 경우 성능이 압도적으로 빠르다. (초저지연 시스템에서 주로 사용)

* 단점: CPU를 100% 점유하며 전력을 낭비하고, 하이퍼스레딩(Hyper-threading) 환경에서는 같은 물리적 코어를 사용하는 다른 논리적 스레드의 실행 자원까지 빼앗아 전체 시스템 성능을 저하시킬 수 있다.

## 3. Thread.onSpinWait() 역할
기존에는 스핀 루프의 단점을 완화할 방법이 없었다. 그래서 Java 9에서 `Thread.onSpinWait()`가 등장 하였다.

```java
volatile boolean isReady = false;

while (!isReady) {
    Thread.onSpinWait(); // "의미 없이 루프 도는 중이니까 최적화 좀 해줘" 라는 힌트
}
```

이 메서드를 호출하면 JVM은 이 코드를 운영체제(OS)가 아닌 CPU 하드웨어 수준의 특정 명령어로 변환한다. (예: x86 아키텍처의 PAUSE 명령어, ARM의 YIELD 명령어)

## 4. 호출 시 얻게 되는 이점 (CPU 레벨 최적화)
이 힌트를 받은 CPU는 다음과 같은 최적화를 수행한다.

* 전력 소모 감소: 루프를 도는 동안 불필요한 전력 소모를 줄여 발열을 제어한다.

* 하이퍼스레드 성능 향상: 물리적 코어 하나를 두 개의 스레드가 공유할 때, 스핀 대기 중인 스레드가 CPU의 연산 자원(ALU 등)을 덜 사용하게 만들어 다른 스레드가 더 빠르게 작업을 처리할 수 있도록 양보한다.

* 파이프라인 플러시(Pipeline Flush) 방지: 무의미한 메모리 읽기를 반복하다가 루프를 빠져나올 때 발생할 수 있는 파이프라인의 병목 현상을 방지하여, 조건이 만족되었을 때 코드를 지연 없이 즉시 실행할 수 있게 해준다.

## 5. 다른 메서드들과 차이점

| 메서드 | 설명 | Context Switching 발생 여부 |
|:-----:|:-----:|:-----:|
| Thread.sleep(n) | 지정된 시간 동안 스레드를 완전히 잠재운다. OS 스케줄러가 개입한다. | 발생함 (비용이 큼) |
| Thread.yield() | 현재 할당된 CPU 시간을 포기하고 다른 스레드에게 양보한다. | 발생할 수 있음 |
| Thread.onSpinWait() | 스레드는 계속 실행 상태(RUNNABLE)를 유지한다. 하드웨어 수준에서 루프만 최적화한다. | 발생하지 않음 (매우 빠름) |

## 6. 주의사항

* 단순한 힌트이다. 이 메서드는 프로그램의 동작 로직(Semantics)을 전혀 바꾸지 않는다. 하드웨어가 이를 지원하지 않으면 JVM은 이 호출을 그냥 무시(No-op)해버린다.

* 일반적인 웹 애플리케이션(Spring Boot 등) 환경에서는 동기화가 필요할 때 wait/notify나 ReentrantLock을 사용한다.

* 밀리초(ms) 단위도 아까운 초저지연(Ultra Low-Latency) 금융 거래 시스템이나, LMAX Disruptor 같은 고성능 동시성 프레임워크, JDK 내부의 병렬 처리 라이브러리(`ConcurrentHashMap`, `Phaser` 등) 성능을 극한으로 끌어올리기 위해 사용한다.

## [출처 및 참고]
* [https://openjdk.org/jeps/285](https://openjdk.org/jeps/285)
* [https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Thread.html#onSpinWait()](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Thread.html#onSpinWait())
