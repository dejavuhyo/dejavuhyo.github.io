---
title: Java Collections.synchronizedMap과 ConcurrentHashMap 차이점
author: dejavuhyo
date: 2023-04-17 09:10:00 +0900
tags: [java-synchronizedmap, java-concurrenthashmap, synchronizedmap-vs-concurrenthashmap, synchronizedmap-concurrenthashmap-차이점]
categories: [Language, Java]
---

## 1. 차이점
`Collections.synchronizedMap()` 및 `ConcurrentHashMap`은 모두 데이터 컬렉션에 대한 스레드 안전 작업을 제공한다.

Collections 유틸리티 클래스는 **컬렉션에서 작동하고 래핑된 컬렉션을 반환하는 다형성 알고리즘을 제공한다.** 해당 `synchronizedMap()` 메서드는 스레드로부터 안전한 기능을 제공한다.

이름에서 알 수 있듯이 `synchronizedMap()`은 매개변수에서 제공하는 Map이 지원 하는 동기화된 Map을 반환한다. 스레드 안전성을 제공하기 위해 `synchronizedMap()`은 반환된 Map을 통해 백업 Map에 대한 모든 액세스를 허용한다.

`ConcurrentHashMap`은 **검색 및 업데이트에 대한 높은 동시성을 지원하는 HashMap의 향상 기능으로 JDK 1.5에 도입되었다.** HashMap은 스레드로부터 안전하지 않으므로 스레드 경합 중에 잘못된 결과가 발생할 수 있다.

`ConcurrentHashMap` 클래스는 스레드로부터 안전하다. 따라서 여러 스레드가 복잡한 문제 없이 단일 개체에서 작동할 수 있다.

**`ConcurrentHashMap`에서 읽기 작업은 차단되지 않는 반면 쓰기 작업은 특정 세그먼트 또는 버킷을 잠근다.** 기본 버킷 또는 동시성 수준은 16이며, 이는 16개의 스레드가 세그먼트 또는 버킷을 잠근 후 언제든지 쓸 수 있음을 의미한다.

### 1) ConcurrentModificationException
HashMap과 같은 객체의 경우 동시 작업 수행이 허용되지 않는다. 따라서 반복하는 동안 HashMap을 업데이트하려고 하면 ConcurrentModificationException을 수신하게 된다. 이것은 `synchronizedMap()`을 사용할 때도 발생한다.

```java
@Test(expected = ConcurrentModificationException.class)
public void whenRemoveAndAddOnHashMap_thenConcurrentModificationError() {
    Map<Integer, String> map = new HashMap<>();
    map.put(1, "baeldung");
    map.put(2, "HashMap");
    Map<Integer, String> synchronizedMap = Collections.synchronizedMap(map);
    Iterator<Entry<Integer, String>> iterator = synchronizedMap.entrySet().iterator();
    while (iterator.hasNext()) {
        synchronizedMap.put(3, "Modification");
        iterator.next();
    }
}
```

그러나 이것은 ConcurrentHashMap의 경우가 아니다.

```java
Map<Integer, String> map = new ConcurrentHashMap<>();
map.put(1, "baeldung");
map.put(2, "HashMap");
 
Iterator<Entry<Integer, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
    map.put(3, "Modification");
    iterator.next()
}
 
Assert.assertEquals(3, map.size());
```

### 2) null 지원
`Collections.synchronizedMap()`과 `ConcurrentHashMap`은 **null 키와 값을 다르게 처리한다.**

ConcurrentHashMap은 키 또는 값에서 null을 허용하지 않는다.

```java
@Test(expected = NullPointerException.class)
public void allowNullKey_In_ConcurrentHasMap() {
    Map<String, Integer> map = new ConcurrentHashMap<>();
    map.put(null, 1);
}
```

그러나 `Collections.synchronizedMap()`을 사용할 때 null 지원은 입력 Map에 따라 다르다. `Collections.synchronizedMap()`이 HashMap 또는 LinkedHashMap에 의해 지원되는 경우 키로 하나의 null을 가질 수 있고 null 값을 얼마든지 가질 수 있다. 반면에 TreeMap을 사용하는 경우에는 null 값을 가질 수 있지만 null 키는 가질 수 없다.

HashMap이 지원하는 `Collections.synchronizedMap()`에 대해 null 키를 사용할 수 있다고 가정해 본다.

```java
Map<String, Integer> map = Collections
  .synchronizedMap(new HashMap<String, Integer>());
map.put(null, 1);
Assert.assertTrue(map.get(null).equals(1));
```

마찬가지로 `Collections.synchronizedMap()` 및 `ConcurrentHashMap` 값에서 null 지원을 확인할 수 있다.

### 3) 성능 비교
`ConcurrentHashMap`과 `Collections.synchronizedMap()`의 성능을 비교해 본다. 이 경우에는 오픈 소스 프레임워크 [JMH(Java Microbenchmark Harness)](https://www.baeldung.com/java-microbenchmark-harness)를 사용하여 방법의 성능을 나노초 단위로 비교한다.

이 맵에서 임의 읽기 및 쓰기 작업에 대한 비교를 실행했다.

```java
@Benchmark
public void randomReadAndWriteSynchronizedMap() {
    Map<String, Integer> map = Collections.synchronizedMap(new HashMap<String, Integer>());
    performReadAndWriteTest(map);
}

@Benchmark
public void randomReadAndWriteConcurrentHashMap() {
    Map<String, Integer> map = new ConcurrentHashMap<>();
    performReadAndWriteTest(map);
}

private void performReadAndWriteTest(final Map<String, Integer> map) {
    for (int i = 0; i < TEST_NO_ITEMS; i++) {
        Integer randNumber = (int) Math.ceil(Math.random() * TEST_NO_ITEMS);
        map.get(String.valueOf(randNumber));
        map.put(String.valueOf(randNumber), randNumber);
    }
}
```

1,000개 항목에 대해 10개의 스레드로 5번의 반복을 사용하여 성능 벤치마크를 실행했다. 벤치마크 결과이다.

```text
Benchmark                                                     Mode  Cnt        Score        Error  Units
MapPerformanceComparison.randomReadAndWriteConcurrentHashMap  avgt  100  3061555.822 ±  84058.268  ns/op
MapPerformanceComparison.randomReadAndWriteSynchronizedMap    avgt  100  3234465.857 ±  60884.889  ns/op
MapPerformanceComparison.randomReadConcurrentHashMap          avgt  100  2728614.243 ± 148477.676  ns/op
MapPerformanceComparison.randomReadSynchronizedMap            avgt  100  3471147.160 ± 174361.431  ns/op
MapPerformanceComparison.randomWriteConcurrentHashMap         avgt  100  3081447.009 ±  69533.465  ns/op
MapPerformanceComparison.randomWriteSynchronizedMap           avgt  100  3385768.422 ± 141412.744  ns/op
```

위의 결과는 `ConcurrentHashMap`이 `Collections.synchronizedMap()`보다 성능이 우수함을 보여준다.

### 4) 사용시기
데이터 일관성이 가장 중요한 경우 `Collections.synchronizedMap()`을 선호해야 하며 읽기 작업보다 쓰기 작업이 훨씬 더 많은 성능이 중요한 애플리케이션의 경우 `ConcurrentHashMap`을 선택해야 한다.

이는 `Collections.synchronizedMap()`이 각 스레드가 읽기/쓰기 작업 모두에 대해 전체 개체에 대한 잠금을 획득해야 하기 때문이다. 이에 비해 `ConcurrentHashMap`을 사용하면 스레드가 컬렉션의 개별 세그먼트에 대한 잠금을 획득하고 동시에 수정할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-synchronizedmap-vs-concurrenthashmap](https://www.baeldung.com/java-synchronizedmap-vs-concurrenthashmap)
