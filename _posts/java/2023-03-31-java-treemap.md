---
title: Java TreeMap
author: dejavuhyo
date: 2023-03-31 08:30:00 +0900
categories: [Language, Java]
tags: [java-treemap, treemap, treemap-guide, 자바-트리맵, 트리맵, 트리맵-가이드]
---

## 1. TreeMap의 기본 정렬
기본적으로 TreeMap은 자연 순서에 따라 모든 항목을 정렬한다. 정수의 경우 이것은 오름차순을 의미하고 문자열의 경우 알파벳 순서를 의미한다.

```java
@Test
public void givenTreeMap_whenOrdersEntriesNaturally_thenCorrect() {
    TreeMap<Integer, String> map = new TreeMap<>();
    map.put(3, "val");
    map.put(2, "val");
    map.put(1, "val");
    map.put(5, "val");
    map.put(4, "val");

    assertEquals("[1, 2, 3, 4, 5]", map.keySet().toString());
}
```

비정렬 방식으로 정수 키를 배치했지만 키 세트를 검색할 때 실제로 오름차순으로 유지되고 있음을 확인한다. 이것은 정수의 자연스러운 순서이다.

마찬가지로 문자열을 사용할 때 문자열은 자연스러운 순서, 즉 알파벳순으로 정렬된다.

```java
@Test
public void givenTreeMap_whenOrdersEntriesNaturally_thenCorrect2() {
    TreeMap<String, String> map = new TreeMap<>();
    map.put("c", "val");
    map.put("b", "val");
    map.put("a", "val");
    map.put("e", "val");
    map.put("d", "val");

    assertEquals("[a, b, c, d, e]", map.keySet().toString());
}
```

TreeMap은 해시맵 및 연결된 해시맵과 달리 배열을 사용하여 항목을 저장하지 않기 때문에 해싱 원칙을 사용하지 않는다.

## 2. TreeMap의 사용자 지정 정렬
TreeMap의 자연스러운 순서가 만족스럽지 않으면 트리맵을 구성하는 동안 비교기를 통해 순서 지정에 대한 고유한 규칙을 정의할 수도 있다.

아래 예에서는 정수 키를 내림차순으로 정렬한다.

```java
@Test
public void givenTreeMap_whenOrdersEntriesByComparator_thenCorrect() {
    TreeMap<Integer, String> map = 
      new TreeMap<>(Comparator.reverseOrder());
    map.put(3, "val");
    map.put(2, "val");
    map.put(1, "val");
    map.put(5, "val");
    map.put(4, "val");
        
    assertEquals("[5, 4, 3, 2, 1]", map.keySet().toString());
}
```

해시맵은 저장된 키의 순서를 보장하지 않으며 특히 이 순서가 시간이 지나도 동일하게 유지된다고 보장하지 않지만 트리맵은 키가 항상 지정된 순서에 따라 정렬되도록 보장한다.

## 3. TreeMap 정렬의 중요성
TreeMap은 모든 항목을 정렬된 순서로 저장한다. 트리맵의 이러한 속성으로 인해 다음과 같은 쿼리를 수행할 수 있다. "가장 큰" 찾기, "가장 작은" 찾기, "특정 값보다 작거나 큰 모든 키" 찾기 등이다.

```java
@Test
public void givenTreeMap_whenPerformsQueries_thenCorrect() {
    TreeMap<Integer, String> map = new TreeMap<>();
    map.put(3, "val");
    map.put(2, "val");
    map.put(1, "val");
    map.put(5, "val");
    map.put(4, "val");
        
    Integer highestKey = map.lastKey();
    Integer lowestKey = map.firstKey();
    Set<Integer> keysLessThan3 = map.headMap(3).keySet();
    Set<Integer> keysGreaterThanEqTo3 = map.tailMap(3).keySet();

    assertEquals(new Integer(5), highestKey);
    assertEquals(new Integer(1), lowestKey);
    assertEquals("[1, 2]", keysLessThan3.toString());
    assertEquals("[3, 4, 5]", keysGreaterThanEqTo3.toString());
}
```

## 4. TreeMap 내부 구현
TreeMap은 NavigableMap 인터페이스를 구현 하고 red-black 트리의 원칙에 대한 내부 작업을 기반으로 한다.

```java
public class TreeMap<K,V> extends AbstractMap<K,V>
  implements NavigableMap<K,V>, Cloneable, java.io.Serializable
```

red-black 트리의 원리는 이들이 TreeMap에 어떻게 적용되는지 이해하기 위해 알아야하는 핵심 부분이다.

**red-black 트리는 노드로 구성된 데이터 구조이다.** 하늘에 뿌리가 있고 가지가 아래로 자라는 거꾸로 된 망고 나무를 상상해본다. 루트에는 트리에 추가된 첫 번째 요소가 포함된다.

규칙은 루트에서 시작하여 모든 노드의 왼쪽 분기에 있는 모든 요소가 항상 노드 자체의 요소보다 작다는 것이다. 오른쪽에 있는 것이 항상 더 크다. 보다 크거나 작게 정의하는 것은 요소의 자연적인 순서 또는 앞에서 본 것처럼 구성 시 정의된 비교기에 의해 결정된다.

이 규칙은 트리맵의 항목이 항상 정렬되고 예측 가능한 순서로 유지되도록 보장한다.

**red-black 트리는 자체 균형 이진 검색 트리이다.** 이 속성과 위의 내용은 검색, 가져오기, 넣기 및 제거와 같은 기본 작업에 대수 시간 `O(log n)`이 소요됨을 보장한다.

여기에서 자기 균형을 잡는 것이 핵심이다. 항목을 계속 삽입하고 삭제하면서 한쪽 가장자리는 길어지고 다른 쪽 가장자리는 짧아지는 나무를 상상해본다.

이는 연산이 더 짧은 분기에서는 더 짧은 시간이 걸리고 루트에서 가장 멀리 있는 분기에서는 더 오랜 시간이 걸린다는 것을 의미한다. 이는 원하지 않는 일이다.

따라서 이것은 red-black 트리 디자인에서 처리된다. 모든 삽입 및 삭제에 대해 모든 가장자리에서 트리의 최대 높이는 `O(log n)`에서 유지된다. 즉, 트리는 지속적으로 균형을 유지한다.

해시맵 및 연결된 해시맵과 마찬가지로 트리맵은 동기화되지 않으므로 멀티 스레드 환경에서 사용하는 규칙은 다른 두 맵 구현과 유사하다.

## 5. 올바른 맵 선택
**해시맵은** 빠른 저장 및 검색 작업을 제공하는 범용 맵 구현으로 적합하다. 그러나 항목의 혼란스럽고 무질서한 배열 때문에 부족하다.

이로 인해 기본 배열의 전체 용량이 항목 수 이외의 순회에 영향을 미치므로 반복이 많은 시나리오에서 성능이 저하된다.

**링크된 해시맵은** 해시맵의 좋은 속성을 가지고 있으며 항목에 순서를 추가한다. 용량에 관계없이 항목 수만 고려하기 때문에 반복이 많은 경우 성능이 더 좋다.

**트리맵은** 키 정렬 방식을 완벽하게 제어하여 다음 수준으로 정렬한다. 반대로 다른 두 대안보다 일반적인 성능이 떨어진다.

**링크된 해시맵이 트리맵의 성능 저하 없이 해시맵 순서의 혼돈을 줄여준다고 말할 수 있다.**

## [출처 및 참고]
* [https://www.baeldung.com/java-treemap](https://www.baeldung.com/java-treemap)
