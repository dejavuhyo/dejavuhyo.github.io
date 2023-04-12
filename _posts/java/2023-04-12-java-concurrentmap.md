---
title: Java ConcurrentMap
author: dejavuhyo
date: 2023-04-12 09:35:00 +0900
categories: [Language, Java]
tags: [java-concurrentmap, concurrentmap, 자바-컨커런트맵, 컨커런트맵]
---

## 1. ConcurrentMap
ConcurrentMap은 Map 인터페이스의 확장이다. 스레드 안전성과 처리량을 조정하는 문제를 해결하기 위한 구조 및 지침을 제공하는 것을 목표로 한다.

여러 인터페이스 기본 메서드를 재정의함으로써 ConcurrentMap은 스레드 안전성 및 메모리 일관성 원자 작업을 제공하는 유효한 구현에 대한 지침을 제공한다.

여러 기본 구현이 재정의되어 null key/value 지원이 비활성화된다.

* getOrDefault

* forEach

* replaceAll

* computeIfAbsent

* computeIfPresent

* compute

* merge

기본 인터페이스 구현 없이 원자성을 지원하기 위해 다음 API도 재정의된다.

* putIfAbsent

* remove

* replace(key, oldValue, newValue)

* replace(key, value)

나머지 작업은 기본적으로 Map과 일치하도록 직접 상속된다.

## 2. ConcurrentHashMap
ConcurrentHashMap은 바로 사용할 수 있는 ConcurrentMap 구현이다.

더 나은 성능을 위해 후드 아래에서 테이블 버킷(Java 8 이전의 테이블 세그먼트로 사용됨)으로 노드 배열로 구성되며 주로 업데이트 중에 CAS 작업을 사용한다.

테이블 버킷은 첫 번째 삽입 시 느리게 초기화된다. 버킷의 첫 번째 노드를 잠그면 각 버킷을 독립적으로 잠글 수 있다. 읽기 작업이 차단되지 않고 업데이트 경합이 최소화된다.

필요한 세그먼트 수는 테이블에 액세스하는 스레드 수에 상대적이므로 세그먼트당 진행 중인 업데이트가 대부분의 경우 한 번만 발생한다.

**Java 8 이전에는 필요한 "세그먼트"의 수가 테이블에 액세스하는 스레드 수에 비례하여 세그먼트당 진행 중인 업데이트가 대부분의 경우 하나를 넘지 않았다.**

그렇기 때문에 생성자는 HashMap과 비교하여 추가 concurrencyLevel 인수를 제공하여 사용할 예상 스레드 수를 제어한다.

```java
public ConcurrentHashMap(
```

```java
public ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel)
```

다른 두 인수인 initialCapacity와 loadFactor는 HashMap과 동일하게 작동했다.

그러나 Java 8 부터 구성자는 이전 버전과의 호환성을 위해서만 존재한다. 매개변수는 맵의 초기 크기에만 영향을 미칠 수 있다.

### 1) 스레드 안전성
ConcurrentMap은 멀티스레딩 환경에서 key/value 작업에 대한 메모리 일관성을 보장한다.

객체를 키 또는 값으로 ConcurrentMap에 배치하기 전에 스레드의 작업은 다른 스레드에서 해당 객체에 대한 액세스 또는 제거 이후의 작업 전에 발생한다.

확인을 위한 메모리 불일치 사례이다.

```java
@Test
public void givenHashMap_whenSumParallel_thenError() throws Exception {
    Map<String, Integer> map = new HashMap<>();
    List<Integer> sumList = parallelSum100(map, 100);

    assertNotEquals(1, sumList
      .stream()
      .distinct()
      .count());
    long wrongResultCount = sumList
      .stream()
      .filter(num -> num != 100)
      .count();
    
    assertTrue(wrongResultCount > 0);
}

private List<Integer> parallelSum100(Map<String, Integer> map, 
  int executionTimes) throws InterruptedException {
    List<Integer> sumList = new ArrayList<>(1000);
    for (int i = 0; i < executionTimes; i++) {
        map.put("test", 0);
        ExecutorService executorService = 
          Executors.newFixedThreadPool(4);
        for (int j = 0; j < 10; j++) {
            executorService.execute(() -> {
                for (int k = 0; k < 10; k++)
                    map.computeIfPresent(
                      "test", 
                      (key, value) -> value + 1
                    );
            });
        }
        executorService.shutdown();
        executorService.awaitTermination(5, TimeUnit.SECONDS);
        sumList.add(map.get("test"));
    }
    return sumList;
}
```

각 map.computeIfPresent 작업에 대해 HashMap은 현재 정수 값이어야 하는 것에 대한 일관된 보기를 제공하지 않아 일관되지 않고 바람직하지 않은 결과를 초래한다.

ConcurrentHashMap의 경우 일관되고 올바른 결과를 얻을 수 있다.

```java
@Test
public void givenConcurrentMap_whenSumParallel_thenCorrect() 
  throws Exception {
    Map<String, Integer> map = new ConcurrentHashMap<>();
    List<Integer> sumList = parallelSum100(map, 1000);

    assertEquals(1, sumList
      .stream()
      .distinct()
      .count());
    long wrongResultCount = sumList
      .stream()
      .filter(num -> num != 100)
      .count();
    
    assertEquals(0, wrongResultCount);
}
```

### 2) Null Key/Value
ConcurrentMap에서 제공하는 대부분의 API는 null 키 또는 값을 허용하지 않는다.

```java
@Test(expected = NullPointerException.class)
public void givenConcurrentHashMap_whenPutWithNullKey_thenThrowsNPE() {
    concurrentMap.put(null, new Object());
}

@Test(expected = NullPointerException.class)
public void givenConcurrentHashMap_whenPutNullValue_thenThrowsNPE() {
    concurrentMap.put("test", null);
}
```

그러나 compute 및 merge 작업의 경우 계산된 값은 null 일 수 있다. 이는 key-value 매핑이 있는 경우 제거되거나 이전에 없는 경우 없는 상태로 남아 있음을 나타낸다.

```java
@Test
public void givenKeyPresent_whenComputeRemappingNull_thenMappingRemoved() {
    Object oldValue = new Object();
    concurrentMap.put("test", oldValue);
    concurrentMap.compute("test", (s, o) -> null);

    assertNull(concurrentMap.get("test"));
}
```

### 3) 스트림 지원
Java 8은 ConcurrentHashMap에서도 Stream 지원을 제공한다.

대부분의 스트림 방법과 달리 대량(순차 및 병렬) 작업은 동시 수정을 안전하게 허용한다. Iterator에도 적용되는 ConcurrentModificationException이 발생하지 않는다. 스트림과 관련하여 여러 forEach, search 및 reduce 메서드도 추가되어 더 풍부한 순회 및 맵 축소 작업을 지원한다.

### 4) 성능
내부적으로 ConcurrentHashMap은 해시 테이블을 기반으로 데이터 액세스 및 업데이트가 있는 HashMap과 다소 유사하다(더 복잡하지만).

물론 ConcurrentHashMap은 데이터 검색 및 업데이트에 대한 대부분의 동시 사례에서 훨씬 더 나은 성능을 제공해야 한다.

get 및 put 성능에 대한 빠른 마이크로 벤치마크를 작성하고 이를 Hashtable 및 Collections.synchronizedMap과 비교한다. 두 작업을 4개의 스레드에서 500,000번 실행한다.

```java
@Test
public void givenMaps_whenGetPut500KTimes_thenConcurrentMapFaster() 
  throws Exception {
    Map<String, Object> hashtable = new Hashtable<>();
    Map<String, Object> synchronizedHashMap = 
      Collections.synchronizedMap(new HashMap<>());
    Map<String, Object> concurrentHashMap = new ConcurrentHashMap<>();

    long hashtableAvgRuntime = timeElapseForGetPut(hashtable);
    long syncHashMapAvgRuntime = 
      timeElapseForGetPut(synchronizedHashMap);
    long concurrentHashMapAvgRuntime = 
      timeElapseForGetPut(concurrentHashMap);

    assertTrue(hashtableAvgRuntime > concurrentHashMapAvgRuntime);
    assertTrue(syncHashMapAvgRuntime > concurrentHashMapAvgRuntime);
}

private long timeElapseForGetPut(Map<String, Object> map) 
  throws InterruptedException {
    ExecutorService executorService = 
      Executors.newFixedThreadPool(4);
    long startTime = System.nanoTime();
    for (int i = 0; i < 4; i++) {
        executorService.execute(() -> {
            for (int j = 0; j < 500_000; j++) {
                int value = ThreadLocalRandom
                  .current()
                  .nextInt(10000);
                String key = String.valueOf(value);
                map.put(key, value);
                map.get(key);
            }
        });
    }
    executorService.shutdown();
    executorService.awaitTermination(1, TimeUnit.MINUTES);
    return (System.nanoTime() - startTime) / 500_000;
}
```

마이크로 벤치마크는 단일 시나리오만 보고 실제 성능을 항상 잘 반영하는 것은 아니다.

즉, 평균 개발 시스템이 있는 OS X 시스템에서 100회 연속 실행(나노초 단위)에 대한 평균 샘플 결과를 볼 수 있다.

```text
Hashtable: 1142.45
SynchronizedHashMap: 1273.89
ConcurrentHashMap: 230.2
```

다중 스레드가 공통 Map에 액세스할 것으로 예상되는 다중 스레드 환경에서는 ConcurrentHashMap이 확실히 선호된다.

그러나 단일 스레드에서만 Map에 액세스할 수 있는 경우 단순성과 견고한 성능을 위해 HashMap이 더 나은 선택이 될 수 있다.

### 5) Pitfalls
검색 작업은 일반적으로 ConcurrentHashMap에서 차단되지 않으며 업데이트 작업과 겹칠 수 있다. 따라서 더 나은 성능을 위해 [공식 Javadoc](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html)에 명시된 대로 가장 최근에 완료된 업데이트 작업의 결과만 반영한다.

주의해야 할 몇 가지 다른 사실이 있다.

* size, isEmpty 및 containsValue를 포함한 집계 상태 메서드의 결과는 일반적으로 맵이 다른 스레드에서 동시 업데이트를 진행하지 않는 경우에만 유용하다.

```java
@Test
public void givenConcurrentMap_whenUpdatingAndGetSize_thenError() 
  throws InterruptedException {
    Runnable collectMapSizes = () -> {
        for (int i = 0; i < MAX_SIZE; i++) {
            mapSizes.add(concurrentMap.size());
        }
    };
    Runnable updateMapData = () -> {
        for (int i = 0; i < MAX_SIZE; i++) {
            concurrentMap.put(String.valueOf(i), i);
        }
    };
    executorService.execute(updateMapData);
    executorService.execute(collectMapSizes);
    executorService.shutdown();
    executorService.awaitTermination(1, TimeUnit.MINUTES);

    assertNotEquals(MAX_SIZE, mapSizes.get(MAX_SIZE - 1).intValue());
    assertEquals(MAX_SIZE, concurrentMap.size());
}
```

동시 업데이트가 엄격하게 제어되는 경우 집계 상태는 여전히 신뢰할 수 있다.

이러한 집계 상태 방법이 실시간 정확도를 보장하지는 않지만 모니터링 또는 추정 목적에 적합할 수 있다.

ConcurrentHashMap의 `size()` 사용은 `mappingCount()`로 대체되어야 한다. 후자의 메서드는 긴 카운트를 반환하지만 깊이는 동일한 추정을 기반으로 한다.

* hashCode 문제: 정확히 동일한 `hashCode()`를 가진 많은 키를 사용하는 것은 모든 해시 테이블의 성능을 저하시키는 확실한 방법이다. 키가 Comparable 일 때 영향을 완화하기 위해 ConcurrentHashMap은 키 간의 비교 순서를 사용하여 관계를 끊을 수 있다. 그래도 가능한 한 동일한 `hashCode()`를 사용하지 않아야 한다.

* iterator는 fast-fail traversal보다 약한 일관성을 제공하고 ConcurrentModificationException을 발생시키지 않기 때문에 단일 스레드에서만 사용하도록 설계되었다.

* 기본 초기 테이블 용량은 16이며 지정된 동시성 수준으로 조정된다.

```java
public ConcurrentHashMap(
  int initialCapacity, float loadFactor, int concurrencyLevel) {
 
    //...
    if (initialCapacity < concurrencyLevel) {
        initialCapacity = concurrencyLevel;
    }
    //...
}
```

* 함수 다시 매핑에 대한 주의: 제공된 compute 및 merge 메서드로 다시 매핑 작업을 수행할 수 있지만 빠르고 짧고 단순하게 유지하고 예기치 않은 차단을 방지하기 위해 현재 매핑에 집중해야 한다.

* ConcurrentHashMap의 키는 정렬된 순서가 아니므로 정렬이 필요한 경우에는 ConcurrentSkipListMap이 적합하다.

## 3. ConcurrentNavigableMap
키 순서 지정이 필요한 경우에는 TreeMap의 동시 버전인 ConcurrentSkipListMap을 사용할 수 있다.

ConcurrentMap에 대한 보충으로 ConcurrentNavigableMap은 키의 전체 순서(기본적으로 오름차순)를 지원하고 동시에 탐색할 수 있다. 지도의 뷰를 반환하는 메서드는 동시성 호환성을 위해 재정의된다.

* subMap

* headMap

* tailMap

* subMap

* headMap

* tailMap

* descendingMap

`keySet()` 뷰의 반복자와 분할자는 약한 메모리 일관성으로 향상된다.

* navigableKeySet

* keySet

* descendingKeySet

## 4. ConcurrentSkipListMap
ConcurrentSkipListMap은 TreeMap의 확장 가능한 동시 버전으로 볼 수 있다.

실제로는 Java에서 red-black 트리를 동시에 구현하지 않는다. SkipLists의 동시 변형은 ConcurrentSkipListMap에서 구현되어 containsKey, get, put 및 remove 작업 및 변형에 대한 예상 평균 `log(n)` 시간 비용을 제공한다.

TreeMap의 기능 외에도 키 삽입, 제거, 업데이트 및 액세스 작업은 스레드 안전성으로 보장된다. 다음은 동시에 탐색할 때 TreeMap과 비교한 것이다.

```java
@Test
public void givenSkipListMap_whenNavConcurrently_thenCountCorrect() 
  throws InterruptedException {
    NavigableMap<Integer, Integer> skipListMap
      = new ConcurrentSkipListMap<>();
    int count = countMapElementByPollingFirstEntry(skipListMap, 10000, 4);
 
    assertEquals(10000 * 4, count);
}

@Test
public void givenTreeMap_whenNavConcurrently_thenCountError() 
  throws InterruptedException {
    NavigableMap<Integer, Integer> treeMap = new TreeMap<>();
    int count = countMapElementByPollingFirstEntry(treeMap, 10000, 4);
 
    assertNotEquals(10000 * 4, count);
}

private int countMapElementByPollingFirstEntry(
  NavigableMap<Integer, Integer> navigableMap, 
  int elementCount, 
  int concurrencyLevel) throws InterruptedException {
 
    for (int i = 0; i < elementCount * concurrencyLevel; i++) {
        navigableMap.put(i, i);
    }
    
    AtomicInteger counter = new AtomicInteger(0);
    ExecutorService executorService
      = Executors.newFixedThreadPool(concurrencyLevel);
    for (int j = 0; j < concurrencyLevel; j++) {
        executorService.execute(() -> {
            for (int i = 0; i < elementCount; i++) {
                if (navigableMap.pollFirstEntry() != null) {
                    counter.incrementAndGet();
                }
            }
        });
    }
    executorService.shutdown();
    executorService.awaitTermination(1, TimeUnit.MINUTES);
    return counter.get();
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-concurrent-map](https://www.baeldung.com/java-concurrent-map)
