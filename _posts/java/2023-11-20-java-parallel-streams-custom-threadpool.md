---
title: Java 병렬 스트림의 사용자 정의 스레드 풀
author: dejavuhyo
date: 2023-11-20 21:15:00 +0900
categories: [Language, Java]
tags: [java-stream, parallel-streams, thread-pools, 자바-스트림, 병렬-스트림, 스레드-풀]
---

## 1. 병렬 스트림
임의의 Collection 유형에 대해 parallelStream 메서드를 호출하면 병렬 Stream이 반환될 수 있다.

```java
@Test
public void givenList_whenCallingParallelStream_shouldBeParallelStream(){
    List<Long> aList = new ArrayList<>();
    Stream<Long> parallelStream = aList.parallelStream();
        
    assertTrue(parallelStream.isParallel());
}
```

이러한 Stream에서 발생하는 기본 처리는 전체 애플리케이션이 공유하는 스레드 풀인 `ForkJoinPool.commonPool()`을 사용한다.

## 2. 커스텀 스레드 풀
실제로 스트림을 처리할 때 사용자 정의 ThreadPool을 전달할 수 있다.

병렬 스트림이 사용자 정의 ThreadPool을 사용하여 1부터 1,000,000까지의 긴 값의 합계를 계산하도록 한다.

```java
@Test
public void giveRangeOfLongs_whenSummedInParallel_shouldBeEqualToExpectedTotal() 
  throws InterruptedException, ExecutionException {
    
    long firstNum = 1;
    long lastNum = 1_000_000;

    List<Long> aList = LongStream.rangeClosed(firstNum, lastNum).boxed()
      .collect(Collectors.toList());

    ForkJoinPool customThreadPool = new ForkJoinPool(4);
    long actualTotal = customThreadPool.submit(
      () -> aList.parallelStream().reduce(0L, Long::sum)).get();
 
    assertEquals((lastNum + firstNum) * lastNum / 2, actualTotal);
}
```

병렬 처리 수준이 4인 ForkJoinPool 생성자를 사용했다. 다양한 환경에 대한 최적의 값을 결정하려면 몇 가지 실험이 필요하지만 경험상 좋은 규칙은 단순히 CPU에 있는 코어 수를 기준으로 숫자를 선택하는 것이다.

다음으로 병렬 Stream의 내용을 처리하여 축소 호출로 요약했다.

이 간단한 예는 사용자 정의 스레드 풀 사용의 전체 유용성을 보여주지는 못할 수 있지만 네트워크 소스의 데이터 처리와 같은 장기 실행 작업과 공통 스레드 풀을 묶고 싶지 않은 상황에서는 이점이 분명해진다. 또는 응용 프로그램 내의 다른 구성 요소에서 공통 스레드 풀을 사용하고 있다.

위의 테스트 메서드를 실행하면 통과한다.

그러나 테스트 메소드에서와 동일한 방식으로 일반 메소드에서 ForkJoinPool 클래스를 인스턴스화하면 OutOfMemoryError가 발생할 수 있다.

다음으로 메모리 누수의 원인을 자세히 살펴본다.

## 3. 메모리 누수 주의
공통 스레드 풀은 기본적으로 전체 애플리케이션에서 사용된다. 공통 스레드 풀은 정적 ThreadPool 인스턴스이다.

따라서 기본 스레드 풀을 사용하면 메모리 누수가 발생하지 않는다.

테스트 메소드에서는 ForkJoinPool 객체를 생성했다. 테스트 메소드가 완료되면 customThreadPool 객체는 참조 해제되거나 garbage 수집되지 않는다. 대신 새 작업이 할당될 때까지 기다린다.

즉, 테스트 메소드를 호출할 때마다 새로운 customThreadPool 객체가 생성되고 해제되지 않는다.

문제에 대한 수정은 매우 간단하다. 메서드를 실행한 후 customThreadPool 객체를 종료 한다.

```java
try {
    long actualTotal = customThreadPool.submit(
      () -> aList.parallelStream().reduce(0L, Long::sum)).get();
    assertEquals((lastNum + firstNum) * lastNum / 2, actualTotal);
} finally {
    customThreadPool.shutdown();
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-8-parallel-streams-custom-threadpool](https://www.baeldung.com/java-8-parallel-streams-custom-threadpool)
