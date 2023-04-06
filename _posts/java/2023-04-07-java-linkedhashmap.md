---
title: Java LinkedHashMap
author: dejavuhyo
date: 2023-04-07 08:25:00 +0900
categories: [Language, Java]
tags: [java-linkedhashmap, linkedhashmap, 자바-링크드해시맵, 링크드해시맵]
---

## 1. LinkedHashMap vs HashMap
LinkedHashMap 클래스는 대부분의 측면에서 HashMap과 매우 유사하다. 그러나 링크된 해시맵은 해시맵의 기능을 향상시키기 위해 해시 테이블과 링크된 목록을 모두 기반으로 한다.

기본 크기 16의 기본 배열 외에도 모든 항목을 통해 실행되는 이중 연결 목록을 유지 관리한다.

요소의 순서를 유지하기 위해 연결된 해시맵은 다음 항목과 이전 항목에 대한 포인터를 추가하여 HashMap의 Map.Entry 클래스를 수정한다.

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after;
    Entry(int hash, K key, V value, Node<K,V> next) {
        super(hash, key, value, next);
    }
}
```

Entry 클래스는 단순히 두 개의 포인터를 추가한다. before와 after에 링크된 목록에 연결되도록 한다. 그 외에도 HashMap의 Entry 클래스 구현을 사용한다.

마지막으로, 이 연결 목록은 반복 순서를 정의하며 기본적으로 요소 삽입 순서(삽입 순서)임을 기억한다.

## 2. 삽입 순서 LinkedHashMap
맵에 삽입되는 방식에 따라 항목의 순서를 지정하는 연결된 해시 맵 인스턴스이다. 또한 맵의 수명 주기 동안 이 순서가 유지되도록 보장한다.

```java
@Test
public void givenLinkedHashMap_whenGetsOrderedKeyset_thenCorrect() {
    LinkedHashMap<Integer, String> map = new LinkedHashMap<>();
    map.put(1, null);
    map.put(2, null);
    map.put(3, null);
    map.put(4, null);
    map.put(5, null);

    Set<Integer> keys = map.keySet();
    Integer[] arr = keys.toArray(new Integer[0]);

    for (int i = 0; i < arr.length; i++) {
        assertEquals(new Integer(i + 1), arr[i]);
    }
}
```

연결된 해시 맵의 항목 순서에 대한 기초적이고 결정적이지 않은 테스트를 수행하고 있다.

게재 신청서가 항상 유지되므로 이 테스트가 항상 통과할 것임을 보장할 수 있다. HashMap에 대해 동일한 보장을 할 수 없다.

이 속성은 모든 맵을 수신하고 조작할 복사본을 만들고 이를 호출 코드로 반환하는 API에서 큰 이점이 될 수 있다. 클라이언트가 API를 호출하기 전에 동일한 방식으로 반환된 맵을 주문해야 하는 경우 링크된 해시맵이 사용된다.

키를 맵에 다시 삽입해도 삽입 순서는 영향을 받지 않는다.

## 3. LinkedHashMap 액세스 주문
LinkedHashMap은 커스텀 로드 팩터(LF)와 초기 용량 중에서 access-order라고 하는 다른 순서 매커니즘/전략을 지정할 수 있는 특수 생성자를 제공한다.

```java
LinkedHashMap<Integer, String> map = new LinkedHashMap<>(16, .75f, true);
```

첫 번째 매개변수는 초기 용량이고 그 다음은 부하율이고 마지막 매개변수는 주문 모드이다. 따라서 true를 전달하여 액세스 순서를 설정했지만 기본값은 삽입 순서이다.

이 메커니즘은 요소의 반복 순서가 가장 최근에 액세스된 것부터 가장 최근에 액세스된 것까지 요소가 마지막으로 액세스된 순서임을 보장한다.

따라서 LRU(최소 최근 사용) 캐시를 구축하는 것은 이러한 종류의 맵에서 매우 쉽고 실용적이다. 넣기 또는 가져오기 작업이 성공 하면 항목에 대한 액세스가 발생한다.

```java
@Test
public void givenLinkedHashMap_whenAccessOrderWorks_thenCorrect() {
    LinkedHashMap<Integer, String> map 
      = new LinkedHashMap<>(16, .75f, true);
    map.put(1, null);
    map.put(2, null);
    map.put(3, null);
    map.put(4, null);
    map.put(5, null);

    Set<Integer> keys = map.keySet();
    assertEquals("[1, 2, 3, 4, 5]", keys.toString());
 
    map.get(4);
    assertEquals("[1, 2, 3, 5, 4]", keys.toString());
 
    map.get(1);
    assertEquals("[2, 3, 5, 4, 1]", keys.toString());
 
    map.get(3);
    assertEquals("[2, 5, 4, 1, 3]", keys.toString());
}
```

맵에서 액세스 작업을 수행할 때 키 집합의 요소 순서가 어떻게 변환되는지 확인한다.

간단히 말해, 맵에 대한 모든 액세스 작업은 반복이 즉시 수행되는 경우 액세스된 요소가 마지막에 표시되는 순서를 따른다.

위의 예 후에는 putAll 작업이 지정된 맵의 각 매핑에 대해 하나의 항목 액세스를 생성한다는 것이 분명해야 한다.

당연히 맵 보기에 대한 반복은 지원 맵의 반복 순서에 영향을 주지 않는다. 맵에 대한 명시적 액세스 작업만 순서에 영향을 미친다.

LinkedHashMap은 또한 고정된 수의 매핑을 유지하고 새 항목을 추가해야 하는 경우 가장 오래된 항목을 계속 삭제하는 메커니즘을 제공한다.

오래된 매핑을 자동으로 제거하기 위해 이 정책을 적용 하기 위해 removeEldestEntry 메서드를 재정의할 수 있다.

이를 실제로 확인하기 위해 LinkedHashMap을 확장하여 오래된 매핑을 강제로 제거할 목적으로만 연결된 해시 맵 클래스를 생성해본다.

```java
public class MyLinkedHashMap<K, V> extends LinkedHashMap<K, V> {

    private static final int MAX_ENTRIES = 5;

    public MyLinkedHashMap(
      int initialCapacity, float loadFactor, boolean accessOrder) {
        super(initialCapacity, loadFactor, accessOrder);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > MAX_ENTRIES;
    }

}
```

위의 재정의를 통해 맵을 최대 5개 항목까지 확장할 수 있다. 크기가 이를 초과하면 맵에서 가장 오래된 항목, 즉 마지막 액세스 시간이 다른 모든 항목보다 앞선 항목을 잃는 대가로 각각의 새 항목이 삽입된다.

```java
@Test
public void givenLinkedHashMap_whenRemovesEldestEntry_thenCorrect() {
    LinkedHashMap<Integer, String> map
      = new MyLinkedHashMap<>(16, .75f, true);
    map.put(1, null);
    map.put(2, null);
    map.put(3, null);
    map.put(4, null);
    map.put(5, null);
    Set<Integer> keys = map.keySet();
    assertEquals("[1, 2, 3, 4, 5]", keys.toString());
 
    map.put(6, null);
    assertEquals("[2, 3, 4, 5, 6]", keys.toString());
 
    map.put(7, null);
    assertEquals("[3, 4, 5, 6, 7]", keys.toString());
 
    map.put(8, null);
    assertEquals("[4, 5, 6, 7, 8]", keys.toString());
}
```

맵에 새 항목을 추가함에 따라 키 세트 시작 부분에서 가장 오래된 항목이 어떻게 계속 떨어지는지 확인한다.

## 4. 성능 고려 사항
HashMap과 마찬가지로 LinkedHashMap은 해시 함수의 크기가 적절하다면 일정한 시간에 추가, 제거 및 포함의 기본 Map 작업을 수행한다. 또한 null 키와 null 값도 허용한다.

그러나 LinkedHashMap의 이러한 일정 시간 성능은 이중 연결 목록을 유지 관리하는 추가 오버헤드로 인해 **HashMap의 일정 시간 성능보다 약간 나쁠 수 있다.**

LinkedHashMap의 컬렉션 뷰에 대한 반복도 HashMap과 비슷한 선형 시간 `O(n)`이 걸린다. 반대로 **LinkedHashMap의 선형 시간 성능은 반복하는 동안 HashMap의 선형 시간 보다 낫다.**

이는 LinkedHashMap의 경우 `O(n)` 의 n은 용량에 관계없이 맵의 항목 수일 뿐이기 때문이다. 반면 HashMap의 경우 n은 용량이고 크기를 합산하면 O(size+capacity)이다.

로드 팩터 및 초기 용량은 HashMap과 같이 정확하게 정의된다. 그러나 이 클래스의 반복 시간은 용량의 영향을 받지 않기 때문에 **초기 용량에 대해 지나치게 높은 값을 선택하는 것에 대한 페널티는 HashMap 보다 LinkedHashMap에서 덜 심각하다.**

## 5. 동시성
HashMap과 마찬가지로 LinkedHashMap 구현은 동기화되지 않는다. 따라서 여러 스레드에서 액세스하려는 경우 이러한 스레드 중 적어도 하나가 구조적으로 변경될 가능성이 있는 경우 외부에서 동기화해야 한다.

생성 시 다음을 수행하는 것이 가장 좋다.

```java
Map m = Collections.synchronizedMap(new LinkedHashMap());
```

HashMap과의 차이점은 구조적 수정을 수반한다는 것이다. 액세스 순서가 지정된 연결된 해시 맵에서 get API를 호출하는 것만으로 구조적 수정이 발생한다. 이와 함께 put 및 remove와 같은 작업이 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-linked-hashmap](https://www.baeldung.com/java-linked-hashmap)
