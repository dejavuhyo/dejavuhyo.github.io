---
title: Java TreeMap vs HashMap
author: dejavuhyo
date: 2023-04-03 09:00:00 +0900
categories: [Language, Java]
tags: [java-hashmap-hashmap, hashmap-hashmap, hashmap-vs-hashmap, 자바-트리맵-해시맵, 트리맵-해시맵, 트리맵-해시맵-비교]
---

## 1. 차이점

### 1) 구현
해시 테이블 기반 구현인 HashMap은 AbstractMap 클래스를 확장하고 Map 인터페이스를 구현한다. HashMap은 해싱 원칙에 따라 작동한다.

이 Map 구현은 일반적으로 버킷 해시 테이블 역할을 하지만 버킷이 너무 커지면 각각 `java.util.TreeMap`의 구조와 유사한 **TreeNodes 노드로 변환된다.**

반면에 TreeMap은 AbstractMap 클래스를 확장하고 NavigableMap 인터페이스를 구현한다. TreeMap은 자체 균형 이진 검색 트리인 Red-Black 트리에 Map 요소를 저장한다.

### 2) Order
HashMap은 요소가 Map에 배열되는 방식에 대해 어떠한 보증도 제공하지 않는다.

즉, HashMap의 키 와 값을 반복하는 동안 순서를 가정할 수 없다.

```java
@Test
public void whenInsertObjectsHashMap_thenRandomOrder() {
    Map<Integer, String> hashmap = new HashMap<>();
    hashmap.put(3, "TreeMap");
    hashmap.put(2, "vs");
    hashmap.put(1, "HashMap");
    
    assertThat(hashmap.keySet(), containsInAnyOrder(1, 2, 3));
}
```

그러나 TreeMap의 항목은 자연 순서에 따라 정렬 된다.

TreeMap 개체를 자연 순서에 따라 정렬할 수 없는 경우 요소가 Map 내에서 정렬되는 순서를 정의하기 위해 Comparator 또는 Comparable을 사용할 수 있다.

```java
@Test
public void whenInsertObjectsTreeMap_thenNaturalOrder() {
    Map<Integer, String> treemap = new TreeMap<>();
    treemap.put(3, "TreeMap");
    treemap.put(2, "vs");
    treemap.put(1, "HashMap");
    
    assertThat(treemap.keySet(), contains(1, 2, 3));
}
```

### 3) Null 값
HashMap은 최대 하나의 null 키 와 많은 null 값을 저장할 수 있다.

```java
@Test
public void whenInsertNullInHashMap_thenInsertsNull() {
    Map<Integer, String> hashmap = new HashMap<>();
    hashmap.put(null, null);
    
    assertNull(hashmap.get(null));
}
```

그러나 TreeMap은 null 키를 허용하지 않지만 많은 null 값을 포함할 수 있다.

`compareTo()` 또는 `compare()` 메서드가 `NullPointerException`을 발생시키기 때문에 null 키는 허용되지 않는다.

```java
@Test(expected = NullPointerException.class)
public void whenInsertNullInTreeMap_thenException() {
    Map<Integer, String> treemap = new TreeMap<>();
    treemap.put(null, "NullPointerException");
}
```

**사용자 정의 Comparator와 함께 TreeMap을 사용하는 경우 null 값을 처리하는 방법은 `compare()` 메서드의 구현에 따라 다르다.**

## 2. 성능 분석
성능은 사용 사례가 주어진 데이터 구조의 적합성을 이해하는데 도움이 되는 가장 중요하다.

### 1) 해시맵
해시 테이블 기반 구현인 HashMap은 내부적으로 배열 기반 데이터 구조를 사용하여 해시 함수에 따라 요소를 구성한다.

HashMap은 `add()`, `remove()` 및 `contains()`와 같은 대부분의 작업에 대해 예상되는 상수 시간 성능 `O(1)`을 제공한다. 따라서 TreeMap 보다 훨씬 빠르다 .

합리적인 가정하에 해시 테이블에서 요소를 검색하는 평균 시간은 `O(1)`이다. 그러나 해시 함수를 잘못 구현하면 버킷의 값 분포가 잘못되어 다음과 같은 결과가 발생할 수 있다.

* 메모리 오버헤드 - 많은 버킷이 사용되지 않은 상태로 남아 있음

* 성능 저하 - 충돌 횟수가 많을수록 성능이 저하됨

**Java 8 이전에는 충돌을 처리하는데 있어서 분리형 연결이 유일하게 선호되는 방법이었다.** 일반적으로 연결된 목록을 사용하여 구현된다. 즉, 충돌이 있거나 두 개의 다른 요소가 동일한 해시 값을 갖는 경우 두 항목을 모두 동일한 연결 목록에 저장한다.

따라서 HashMap에서 요소를 검색하는 것은 최악의 경우 연결된 목록에서 요소를 검색하는 것만큼 오래 걸릴 수 있다. 즉, `O(n)` 시간이다.

그러나 [JEP 180](https://openjdk.org/jeps/180)이 등장하면서 요소가 HashMap에 배열되는 방식의 구현에 미묘한 변화가 있었다.

사양에 따르면 버킷이 너무 커지고 충분한 노드를 포함하면 각 모드가 TreeMap의 구조와 유사한 TreeNodes 모드로 변환된다.

따라서 높은 해시 충돌의 경우 최악의 경우 성능이 `O(n)`에서 `O(log n)`으로 향상된다.

이 변환을 수행하는 코드는 다음과 같다.

```java
if(binCount >= TREEIFY_THRESHOLD - 1) {
    treeifyBin(tab, hash);
}
```

`TREEIFY_THRESHOLD` 의 값은 8이며 버킷에 대한 연결 목록이 아닌 트리를 사용하기 위한 임계값 수를 효과적으로 나타낸다.

* HashMap은 데이터를 보유하는데 필요한 것보다 더 많은 메모리를 필요로 한다.

* HashMap은 `70% - 75%`를 초과해서는 안된다. 가까워지면 크기가 조정되고 항목이 다시 해시된다.

* 재해싱에는 비용이 많이 드는 n 작업이 필요하며 여기서 상수 시간 삽입은 `O(n)` 순서가 된다.

* HashMap에 객체를 삽입하는 순서를 결정하는 해싱 알고리즘이다.

HashMap의 성능은 HashMap 객체 생성 자체에서 사용자 지정 초기 용량 및 부하 계수를 설정하여 조정할 수 있다.

그러나 다음과 같은 경우 HashMap을 선택해야 한다.

* 컬렉션에서 유지해야 할 대략적인 항목 수를 알고 있다.

* 자연스러운 순서로 항목을 추출하고 싶지 않다.

위의 상황에서 HashMap은 일정한 시간 삽입, 검색 및 삭제를 제공하므로 최선의 선택이다.

### 2) 트리맵
TreeMap은 사용자 정의 Comparator의 도움으로 요소를 정렬할 수 있는 기능과 함께 계층적 트리에 데이터를 저장한다.

성능 요약:

* TreeMap은 `add()`, `remove()` 및 `contains()`와 같은 대부분의 작업에 대해 `O(log(n))`의 성능을 제공한다.

* Treemap은 연속 메모리 영역을 사용하는 HashMap과 달리 항목을 보유하는데 필요한 메모리 양만 사용하기 때문에 HashMap과 비교하여 메모리를 절약할 수 있다.

* 트리는 의도한 성능을 유지하기 위해 균형을 유지해야 한다. 이는 상당한 노력이 필요하므로 구현이 복잡하다.

다음과 같은 경우에는 TreeMap을 사용해야 한다.

* 메모리 제한을 고려해야 한다.

* 메모리에 얼마나 많은 항목을 저장해야 하는지 모른다.

* 자연스러운 순서로 개체를 추출하려고 한다.

* 항목이 일관되게 추가 및 제거되는지 여부

* 기꺼이 `O(log n)` 검색 시간을 받아들인다.

## 3. 유사점

### 1) 독특한 요소
TreeMap과 HashMap은 모두 중복 키를 지원하지 않는다. 추가되면 이전 요소를 재정의한다(오류나 예외 없이).

```java
@Test
public void givenHashMapAndTreeMap_whenputDuplicates_thenOnlyUnique() {
    Map<Integer, String> treeMap = new HashMap<>();
    treeMap.put(1, "Baeldung");
    treeMap.put(1, "Baeldung");

    assertTrue(treeMap.size() == 1);

    Map<Integer, String> treeMap2 = new TreeMap<>();
    treeMap2.put(1, "Baeldung");
    treeMap2.put(1, "Baeldung");

    assertTrue(treeMap2.size() == 1);
}
```

### 2) 동시 액세스
두 맵 구현은 동기화 되지 않으며 동시 액세스를 자체적으로 관리해야 한다.

둘 다 여러 스레드가 동시에 액세스하고 스레드 중 적어도 하나가 이를 수정할 때마다 외부에서 동기화되어야 한다.

제공된 맵의 동기화된 보기를 얻으려면 명시적으로 `Collections.synchronizedMap(mapName)`을 사용해야 한다.

### 3) Fail-Fast 반복자
Iterator가 생성된 후 어떤 방식으로든 Map이 수정되면 Iterator는 `ConcurrentModificationException`을 발생 시킨다.

또한 iterator의 remove 메서드를 사용하여 반복 중에 Map을 변경할 수 있다.

```java
@Test
public void whenModifyMapDuringIteration_thenThrowExecption() {
    Map<Integer, String> hashmap = new HashMap<>();
    hashmap.put(1, "One");
    hashmap.put(2, "Two");
    
    Executable executable = () -> hashmap
      .forEach((key,value) -> hashmap.remove(1));
 
    assertThrows(ConcurrentModificationException.class, executable);
}
```

## 4. 구현 선택
일반적으로 두 가지 구현에는 각각의 장단점이 있지만 기본 기대와 요구 사항을 이해하는 것과 관련하여 선택해야 한다.

* 항목을 정렬된 상태로 유지하려면 TreeMap을 사용해야 한다.

* 메모리 소비보다 성능을 우선시한다면 HashMap을 사용해야 한다.

* TreeMap은 더 중요한 지역성을 가지므로 자연스러운 순서에 따라 서로 상대적으로 가까운 개체에 액세스하려는 경우 고려할 수 있다.

* HashMap은 initialCapacity 및 loadFactor를 사용하여 조정할 수 있지만 TreeMap에서는 불가능하다.

* 일정한 시간 액세스의 이점을 누리면서 삽입 순서를 유지하려면 LinkedHashMap을 사용할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-treemap-vs-hashmap](https://www.baeldung.com/java-treemap-vs-hashmap)
