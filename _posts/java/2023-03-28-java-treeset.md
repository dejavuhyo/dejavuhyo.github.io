---
title: Java TreeSet
author: dejavuhyo
date: 2023-03-28 08:50:00 +0900
categories: [Language, Java]
tags: [java-treeset, treeset, treeset-guide, 자바-트리셋, 트리셋, 트리셋-가이드]
---

## 1. TreeSet
간단히 말해서 TreeSet은 AbstractSet 클래스를 확장하고 NavigableSet 인터페이스를 구현하는 정렬된 컬렉션이다.

다음은 이 구현의 가장 중요한 측면에 대한 간략한 요약이다.

* 고유한 elements를 저장한다.

* elements의 삽입 순서를 유지하지 않는다.

* elements를 오름차순으로 정렬한다.

* 스레드로부터 안전하지 않다.

이 구현에서 객체는 자연 순서에 따라 오름차순으로 정렬 및 저장된다. TreeSet은 자체 균형 이진 검색 트리, 특히 [Red-Black 트리](https://www.baeldung.com/cs/red-black-trees)를 사용한다.

간단히 말해 자체 균형 이진 검색 트리인 이진 트리의 각 노드는 빨간색 또는 검은색 노드의 색상을 식별하는데 사용되는 추가 비트로 구성된다. 후속 삽입 및 삭제 중에 이러한 "색상" 비트는 트리가 어느 정도 균형을 유지하는데 도움이 된다.

Tree Set의 인스턴스를 생성한다.

```java
Set<String> treeSet = new TreeSet<>();
```

### 1) 생성자 비교 매개변수가 있는 TreeSet
선택적으로 Comparable 또는 Comparator를 사용하여 elements가 정렬되는 순서를 정의할 수 있는 생성자로 TreeSet을 구성할 수 있다.

```java
Set<String> treeSet = new TreeSet<>(Comparator.comparing(String::length));
```

**TreeSet은 스레드로부터 안전하지 않지만 `Collections.synchronizedSet()` 래퍼를 사용하여 외부에서 동기화할 수 있다.**

```java
Set<String> syncTreeSet = Collections.synchronizedSet(treeSet);
```

## 2. API

### 1) add()
`add()` 메서드는 TreeSet에 elements를 추가하는데 사용할 수 있다. elements가 추가된 경우 메서드는 true를 반환하고 그렇지 않으면 false를 반환한다.

메소드 계약에 따르면 elements는 Set에 동일한 elements가 아직 존재하지 않는 경우에만 추가된다.

TreeSet에 elements를 추가해 본다.

```java
@Test
public void whenAddingElement_shouldAddElement() {
    Set<String> treeSet = new TreeSet<>();

    assertTrue(treeSet.add("String Added"));
}
```

add 메소드는 메소드의 구현 세부 사항이 TreeSet이 내부적으로 어떻게 작동하는지, elememts를 저장하기 위해 TreeMap의 put 메소드를 활용하는 방법을 보여주기 때문에 매우 중요하다.

```java
public boolean add(E e) {
    return m.put(e, PRESENT) == null;
}
```

변수 m은 내부 지원 TreeMap을 참조한다 (TreeMap은 NavigateableMap 구현).

```java
private transient NavigableMap<E, Object> m;
```

따라서 TreeSet은 내부적으로 TreeSet의 인스턴스가 생성될 때 TreeMap의 인스턴스로 초기화되는 백업 NavigableMap에 의존한다.

```java
public TreeSet() {
    this(new TreeMap<E,Object>());
}
```

### 2) contains()
`contains()` 메서드는 주어진 elements가 주어진 TreeSet에 있는지 확인하는데 사용된다. elements가 발견되면 true를 반환하고 그렇지 않으면 false를 반환한다.

```java
@Test
public void whenCheckingForElement_shouldSearchForElement() {
    Set<String> treeSetContains = new TreeSet<>();
    treeSetContains.add("String Added");

    assertTrue(treeSetContains.contains("String Added"));
}
```

### 3) remove()
`remove()` 메서드는 지정된 elements가 있는 경우 집합에서 해당 elements를 제거하는데 사용된다.

집합에 지정된 elements가 포함된 경우 이 메서드는 true를 반환한다.

```java
@Test
public void whenRemovingElement_shouldRemoveElement() {
    Set<String> removeFromTreeSet = new TreeSet<>();
    removeFromTreeSet.add("String Added");

    assertTrue(removeFromTreeSet.remove("String Added"));
}
```

### 4) clear()
집합에서 모든 항목을 제거하려면 `clear()` 메서드를 사용할 수 있다.

```java
@Test
public void whenClearingTreeSet_shouldClearTreeSet() {
    Set<String> clearTreeSet = new TreeSet<>();
    clearTreeSet.add("String Added");
    clearTreeSet.clear();
 
    assertTrue(clearTreeSet.isEmpty());
}
```

### 5) size()
`size()` 메서드는 TreeSet에 있는 elements의 수를 식별하는데 사용된다. API의 기본 메서드 중 하나이다.

```java
@Test
public void whenCheckingTheSizeOfTreeSet_shouldReturnThesize() {
    Set<String> treeSetSize = new TreeSet<>();
    treeSetSize.add("String Added");
 
    assertEquals(1, treeSetSize.size());
}
```

### 6) isEmpty()
`isEmpty()` 메서드는 주어진 TreeSet 인스턴스가 비어 있는지 여부를 파악하는데 사용할 수 있다.

```java
@Test
public void whenCheckingForEmptyTreeSet_shouldCheckForEmpty() {
    Set<String> emptyTreeSet = new TreeSet<>();
    
    assertTrue(emptyTreeSet.isEmpty());
}
```

### 7) iterator()
`iterator()` 메서드는 Set의 elements에 대해 오름차순으로 반복되는 반복자를 반환한다. 이러한 반복자는 `fail-fast`이다.

오름차순 반복 순서를 확인할 수 있다.

```java
@Test
public void whenIteratingTreeSet_shouldIterateTreeSetInAscendingOrder() {
    Set<String> treeSet = new TreeSet<>();
    treeSet.add("First");
    treeSet.add("Second");
    treeSet.add("Third");
    Iterator<String> itr = treeSet.iterator();
    while (itr.hasNext()) {
        System.out.println(itr.next());
    }
}
```

또한 TreeSet을 사용하면 Set을 내림차순으로 반복할 수 있다.

```java
@Test
public void whenIteratingTreeSet_shouldIterateTreeSetInDescendingOrder() {
    TreeSet<String> treeSet = new TreeSet<>();
    treeSet.add("First");
    treeSet.add("Second");
    treeSet.add("Third");
    Iterator<String> itr = treeSet.descendingIterator();
    while (itr.hasNext()) {
        System.out.println(itr.next());
    }
}
```

반복자가 생성된 후 반복자의 `remove()` 메서드를 통하지 않고 어떤 방식으로든 집합이 수정된 경우 반복자는 `ConcurrentModificationException`을 발생시킨다.

이에 대한 테스트이다.

```java
@Test(expected = ConcurrentModificationException.class)
public void whenModifyingTreeSetWhileIterating_shouldThrowException() {
    Set<String> treeSet = new TreeSet<>();
    treeSet.add("First");
    treeSet.add("Second");
    treeSet.add("Third");
    Iterator<String> itr = treeSet.iterator();
    while (itr.hasNext()) {
        itr.next();
        treeSet.remove("Second");
    }
}
```

또는 반복자의 remove 메서드를 사용했다면 예외가 발생하지 않았을 것이다.

```java
@Test
public void whenRemovingElementUsingIterator_shouldRemoveElement() {
 
    Set<String> treeSet = new TreeSet<>();
    treeSet.add("First");
    treeSet.add("Second");
    treeSet.add("Third");
    Iterator<String> itr = treeSet.iterator();
    while (itr.hasNext()) {
        String element = itr.next();
        if (element.equals("Second"))
           itr.remove();
    }
 
    assertEquals(2, treeSet.size());
}
```

**동기화되지 않은 동시 수정이 있는 경우 엄격한 보장을 하는 것이 불가능하므로 반복자의 빠른 실패 동작에 대한 보장이 없다.**

### 8) first()
이 메서드는 비어 있지 않은 경우 TreeSet에서 첫 번째 elements를 반환한다. 그렇지 않으면 `NoSuchElementException`이 발생한다.

```java
@Test
public void whenCheckingFirstElement_shouldReturnFirstElement() {
    TreeSet<String> treeSet = new TreeSet<>();
    treeSet.add("First");
   
    assertEquals("First", treeSet.first());
}
```

### 9) last()
위의 예와 유사하게 이 메서드는 집합이 비어 있지 않은 경우 마지막 elements를 반환한다.

```java
@Test
public void whenCheckingLastElement_shouldReturnLastElement() {
    TreeSet<String> treeSet = new TreeSet<>();
    treeSet.add("First");
    treeSet.add("Last");
    
    assertEquals("Last", treeSet.last());
}
```

### 10) subSet()
이 메서드는 `fromElement`에서 `toElement`까지의 elements를 반환한다. `fromElement`는 포괄적이고 `toElement`는 배타적이다.

```java
@Test
public void whenUsingSubSet_shouldReturnSubSetElements() {
    SortedSet<Integer> treeSet = new TreeSet<>();
    treeSet.add(1);
    treeSet.add(2);
    treeSet.add(3);
    treeSet.add(4);
    treeSet.add(5);
    treeSet.add(6);
    
    Set<Integer> expectedSet = new TreeSet<>();
    expectedSet.add(2);
    expectedSet.add(3);
    expectedSet.add(4);
    expectedSet.add(5);

    Set<Integer> subSet = treeSet.subSet(2, 6);
 
    assertEquals(expectedSet, subSet);
}
```

### 11) headSet()
이 메서드는 지정된 elements보다 작은 TreeSet의 elements를 반환한다.

```java
@Test
public void whenUsingHeadSet_shouldReturnHeadSetElements() {
    SortedSet<Integer> treeSet = new TreeSet<>();
    treeSet.add(1);
    treeSet.add(2);
    treeSet.add(3);
    treeSet.add(4);
    treeSet.add(5);
    treeSet.add(6);

    Set<Integer> subSet = treeSet.headSet(6);
 
    assertEquals(subSet, treeSet.subSet(1, 6));
}
```

### 12) tailSet()
이 메서드는 지정된 elements보다 크거나 같은 TreeSet의 elements를 반환한다.

```java
@Test
public void whenUsingTailSet_shouldReturnTailSetElements() {
    NavigableSet<Integer> treeSet = new TreeSet<>();
    treeSet.add(1);
    treeSet.add(2);
    treeSet.add(3);
    treeSet.add(4);
    treeSet.add(5);
    treeSet.add(6);

    Set<Integer> subSet = treeSet.tailSet(3);
 
    assertEquals(subSet, treeSet.subSet(3, true, 6, true));
}
```

## 3. Null Elements 저장
**Java 7 이전에는 비어 있는 TreeSet에 null elements를 추가할 수 있었다.**

그러나 그것은 버그로 간주되었다. 따라서 **TreeSet은 더 이상 null 추가를 지원하지 않는다.**

TreeSet에 elements를 추가하면 elements는 자연 순서 또는 비교기에 의해 지정된 순서에 따라 정렬된다. 따라서 null을 추가하면 기존 elements와 비교할 때 null을 어떤 값과도 비교할 수 없으므로 `NullPointerException`이 발생한다.

```java
@Test(expected = NullPointerException.class)
public void whenAddingNullToNonEmptyTreeSet_shouldThrowException() {
    Set<String> treeSet = new TreeSet<>();
    treeSet.add("First");
    treeSet.add(null);
}
```

TreeSet에 삽입된 elements는 Comparable 인터페이스를 구현하거나 최소한 지정된 비교기에 의해 승인되어야 한다. 이러한 모든 elements는 상호 비교 가능해야 한다. 즉, `e1.compareTo(e2)` 또는 `comparator.compare(e1, e2)`는 `ClassCastException`을 발생시키지 않아야 한다.

```java
class Element {
    private Integer id;

    // Other methods...
}

Comparator<Element> comparator = (ele1, ele2) -> {
    return ele1.getId().compareTo(ele2.getId());
};

@Test
public void whenUsingComparator_shouldSortAndInsertElements() {
    Set<Element> treeSet = new TreeSet<>(comparator);
    Element ele1 = new Element();
    ele1.setId(100);
    Element ele2 = new Element();
    ele2.setId(200);
    
    treeSet.add(ele1);
    treeSet.add(ele2);
    
    System.out.println(treeSet);
}
```

## 4. TreeSet 성능
HashSet과 비교할 때 TreeSet의 성능은 낮은 편이다. 추가, 제거 및 검색과 같은 작업은 `O(log n)` 시간이 걸리는 반면 n elements를 정렬된 순서로 인쇄하는 것과 같은 작업은 `O(n)` 시간이 필요하다.

TreeSet은 오름차순 또는 내림차순으로 액세스 및 순회할 수 있고 오름차순 작업 및 뷰의 성능이 내림차순 작업보다 빠를 가능성이 있으므로 항목을 정렬된 상태로 유지 하려는 경우 기본 선택이 되어야 한다.

> Locality의 원리 - 메모리 액세스 패턴에 따라 동일한 값 또는 관련 저장 위치가 자주 액세스 되는 현상에 대한 용어이다.

locality를 말할 때:

* 비슷한 빈도로 애플리케이션에서 유사한 데이터에 액세스하는 경우가 많다.

* 두 개의 항목이 순서에 따라 근처에 있는 경우 TreeSet은 데이터 구조에서 서로 가까이 배치하여 메모리에 배치한다.

따라서 지역성의 원칙에 따라 메모리가 부족하고 상대적으로 가까운 elements에 액세스하려는 경우 TreeSet에 우선권을 주어야 한다고 결론을 내릴 수 있다.

데이터를 하드 드라이브에서 읽어야 하는 경우(캐시 또는 메모리에서 읽는 데이터보다 지연 시간이 더 큼) 지역성이 더 큰 TreeSet을 선호한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-tree-set](https://www.baeldung.com/java-tree-set)
