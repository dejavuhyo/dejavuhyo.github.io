---
title: Java HashSet
author: dejavuhyo
date: 2023-02-07 09:00:00 +0900
categories: [Language, Java]
tags: [java-hashset, hashset, hashset-guide, 자바-해시셋, 해시셋, 해시셋-가이드]
---

## 1. HashSet
HashSet은 Java Collections API의 기본 데이터 구조 중 하나이다.

이 구현의 가장 중요한 측면이다.

* 고유한 elements를 저장하고 null을 허용한다.

* HashMap 에 의해 지원된다.

* 삽입 순서를 유지하지 않는다.

* 스레드로부터 안전하지 않다.

이 내부 HashMap은 HashSet의 인스턴스가 생성될 때 초기화된다.

```java
public HashSet() {
    map = new HashMap<>();
}
```

## 2. API

### 1) add()
`add()` 메서드는 집합에 elements를 추가하는 데 사용할 수 있다. 메소드 계약은 elements가 세트에 아직 존재하지 않는 경우에만 elements가 추가된다고 명시한다. elements가 추가된 경우 메서드는 true를 반환하고 그렇지 않으면 false를 반환한다.

다음과 같이 HashSet에 elements를 추가할 수 있다.

```java
@Test
public void whenAddingElement_shouldAddElement() {
    Set<String> hashset = new HashSet<>();
 
    assertTrue(hashset.add("String Added"));
}
```

구현 관점에서 add 메소드는 매우 중요한 메소드이다. 구현 세부 정보는 HashSet이 내부적으로 작동하는 방법과 HashMap의 put 메서드를 활용하는 방법을 보여준다.

```java
public boolean add(E e) {
    return map.put(e, PRESENT) == null;
}
```

맵 변수는 내부 지원 HashMap에 대한 참조이다.

```java
private transient HashMap<E, Object> map;
```

해시 기반 데이터 구조에서 elements가 구성되는 방식을 자세히 이해하려면 먼저 해시 코드에 익숙해지는 것이 좋다.

* HashMap은 기본 용량이 16개 elements인 버킷 배열이다. 각 버킷은 서로 다른 해시 코드 값에 해당한다.

* 여러 개체가 동일한 해시 코드 값을 갖는 경우 단일 버킷에 저장된다.

* 로드 팩터에 도달 하면 새 배열이 이전 배열 크기의 두 배로 생성되고 모든 elements가 다시 해시되고 해당 새 버킷에 재배포된다.

* 값을 검색하려면 키를 해시하고 수정한 다음 해당 버킷으로 이동하고 개체가 둘 이상인 경우 잠재적 연결 목록을 검색한다.

### 2) contains()
`contains()` 메소드의 목적은 주어진 HashSet에 elements가 있는지 확인하는 것이다. elements가 발견되면 true를 반환하고 그렇지 않으면 false를 반환한다.

HashSet에서 elements를 확인할 수 있다 .

```java
@Test
public void whenCheckingForElement_shouldSearchForElement() {
    Set<String> hashsetContains = new HashSet<>();
    hashsetContains.add("String Added");
 
    assertTrue(hashsetContains.contains("String Added"));
}
```

개체가 이 메서드에 전달될 때마다 해시 값이 계산된다. 그런 다음 해당 버킷 위치가 확인되고 순회된다.

### 3) remove()
이 메서드는 지정된 elements가 있는 경우 세트에서 해당 elements를 제거한다. 이 메서드는 집합에 지정된 elements가 포함된 경우 true를 반환한다.

```java
@Test
public void whenRemovingElement_shouldRemoveElement() {
    Set<String> removeFromHashSet = new HashSet<>();
    removeFromHashSet.add("String Added");
 
    assertTrue(removeFromHashSet.remove("String Added"));
}
```

### 4) clear()
세트에서 모든 항목을 제거하려는 경우 이 방법을 사용한다. 기본 구현은 단순히 기본 HashMap에서 모든 elements를 지운다.

```java
@Test
public void whenClearingHashSet_shouldClearHashSet() {
    Set<String> clearHashSet = new HashSet<>();
    clearHashSet.add("String Added");
    clearHashSet.clear();
    
    assertTrue(clearHashSet.isEmpty());
}
```

### 5) size()
이것은 API의 기본 방법 중 하나이다. HashSet에 있는 elements의 수를 식별하는 데 도움이 되므로 많이 사용된다. 기본 구현은 단순히 계산을 HashMap의 `size()` 메서드에 위임한다.

```java
@Test
public void whenCheckingTheSizeOfHashSet_shouldReturnThesize() {
    Set<String> hashSetSize = new HashSet<>();
    hashSetSize.add("String Added");
    
    assertEquals(1, hashSetSize.size());
}
```

### 6) isEmpty()
이 메서드를 사용하여 HashSet의 주어진 인스턴스가 비어 있는지 여부를 파악할 수 있다. 이 메서드는 집합에 elements가 포함되어 있지 않으면 true를 반환한다.

```java
@Test
public void whenCheckingForEmptyHashSet_shouldCheckForEmpty() {
    Set<String> emptyHashSet = new HashSet<>();
    
    assertTrue(emptyHashSet.isEmpty());
}
```

### 7) iterator()
메서드는 Set의 elements에 대한 반복자를 반환한다. elements는 특별한 순서 없이 방문되며 반복자는 `fail-fast` 이다.

무작위 반복 순서를 관찰할 수 있다.

```java
@Test
public void whenIteratingHashSet_shouldIterateHashSet() {
    Set<String> hashset = new HashSet<>();
    hashset.add("First");
    hashset.add("Second");
    hashset.add("Third");
    Iterator<String> itr = hashset.iterator();
    while(itr.hasNext()){
        System.out.println(itr.next());
    }
}
```

**반복자가 생성된 후 반복자 자신의 remove 메서드를 통하지 않고 어떤 방식으로든 집합이 수정되면 반복자는 `ConcurrentModificationException`을 발생시킨다.**

```java
@Test(expected = ConcurrentModificationException.class)
public void whenModifyingHashSetWhileIterating_shouldThrowException() {
 
    Set<String> hashset = new HashSet<>();
    hashset.add("First");
    hashset.add("Second");
    hashset.add("Third");
    Iterator<String> itr = hashset.iterator();
    while (itr.hasNext()) {
        itr.next();
        hashset.remove("Second");
    }
}
```

또는 반복자의 remove 메서드를 사용했다면 예외가 발생하지 않았을 것이다.

```java
@Test
public void whenRemovingElementUsingIterator_shouldRemoveElement() {
 
    Set<String> hashset = new HashSet<>();
    hashset.add("First");
    hashset.add("Second");
    hashset.add("Third");
    Iterator<String> itr = hashset.iterator();
    while (itr.hasNext()) {
        String element = itr.next();
        if (element.equals("Second"))
            itr.remove();
    }
 
    assertEquals(2, hashset.size());
}
```

**동기화되지 않은 동시 수정이 있는 경우 엄격한 보장을 하는 것이 불가능하므로 반복자의 빠른 실패 동작을 보장할 수 없다.**

`Fail-fast` 반복자는 최선의 노력으로 `ConcurrentModificationException`을 발생시킨다. 따라서 정확성을 위해 이 예외에 의존하는 프로그램을 작성하는 것은 잘못된 것이다.

## 3. HashSet의 고유성 유지
개체를 HashSet에 넣으면 개체의 해시 코드 값을 사용하여 elements가 이미 집합에 없는지 확인한다.

각 해시 코드 값은 계산된 해시 값이 동일한 다양한 elements를 포함할 수 있는 특정 버킷 위치에 해당한다. 그러나 동일한 hashCode를 가진 두 개체는 같지 않을 수 있다.

따라서 동일한 버킷 내의 객체는 `equals()` 메서드를 사용하여 비교된다.

## 4. HashSet의 성능
HashSet의 성능은 주로 초기 용량과 부하율 이라는 두 가지 매개변수의 영향을 받는다.

세트에 elements를 추가할 때 예상되는 시간 복잡도는 `O(1)`이며 최악의 시나리오(하나의 버킷만 있음)에서 `O(n)`으로 떨어질 수 있다. 따라서 **올바른 HashSet의 용량을 유지하는 것이 중요하다.**

> 참고 사항: JDK 8부터 최악의 시간 복잡도는 `O(log*n)` 이다.

부하율은 세트의 크기를 조정해야 하는 최대 채우기 수준을 설명한다.

초기 용량 및 로드 비율에 대한 사용자 지정 값을 사용하여 HashSet을 만들 수도 있다.

```java
Set<String> hashset = new HashSet<>();
Set<String> hashset = new HashSet<>(20);
Set<String> hashset = new HashSet<>(20, 0.5f);
```

첫 번째 경우에는 기본값인 초기 용량 16과 부하 계수 0.75가 사용된다. 두 번째에서는 기본 용량을 재정의하고 세 번째에서는 둘 다 재정의한다.

**초기 용량이 낮으면 공간 복잡성이 줄어들지만 비용이 많이 드는 프로세스인 재해싱 빈도가 증가한다.**

반면에 **높은 초기 용량은 반복 비용과 초기 메모리 소비를 증가시킨다.**

* 높은 초기 용량은 반복이 거의 또는 전혀 없는 많은 항목에 적합하다.

* 낮은 초기 용량은 반복이 많은 소수의 항목에 적합하다.

따라서 둘 사이의 올바른 균형을 맞추는 것이 매우 중요하다. 일반적으로 기본 구현은 최적화되어 있으며 잘 작동한다. 요구 사항에 맞게 이러한 매개변수를 조정해야 할 필요성을 느끼면 신중하게 수행해야 한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-hashset](https://www.baeldung.com/java-hashset)
