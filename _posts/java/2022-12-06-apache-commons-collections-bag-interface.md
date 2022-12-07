---
title: Bag Interface
author: dejavuhyo
date: 2022-12-06 21:00:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, bag-interface, bag, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. Bag Interface (Apache Commons Collections)
Bag은 객체가 컬렉션에 나타나는 횟수를 카운트하는 컬렉션을 정의한다.

예를 들어 Bag에 `{a, a, b, c}`가 포함되어 있으면 `getCount("a")`는 2를 반환하고 `uniqueSet()`은 고유한 값을 반환한다.

### 1) 인터페이스 선언

```java
public interface Bag<E> extends Collection<E>
```

### 2) Methods

| Method | Description |
|:-----:|:-----:|
| `boolean add(E object)` | (Violation) 지정한 개체의 복사본을 Bag에 추가한다. |
| `boolean add(E object, int nCopies)` | 지정한 개체의 복사본을 Bag에 추가한다. |
| `boolean containsAll(Collection<?> coll)` | (Violation) 지정된 컬렉션에 있는 모든 요소가 Bag에 포함된 경우 카디널리티에 따라 true를 반환한다. |
| `int getCount(Object object)` | 현재 Bag에 있는 지정된 개체의 발생 횟수(카디널리티)를 반환한다. |
| `Iterator<E> iterator()` | 카디널리티로 인한 복사본을 포함하여 전체 구성원 집합에 대해 반복자를 반환한다. |
| `boolean remove(Object object)` | (Violation) 지정한 개체의 모든 항목을 Bag에서 제거한다. |
| `boolean remove(Object object, int nCopies)` | 지정한 개체의 nCopies 복사본을 Bag에서 제거한다. |
| `boolean removeAll(Collection<?> coll)` | (Violation) 카디널리티와 관련하여 지정된 컬렉션에 표시된 모든 요소를 제거한다. |
| `boolean retainAll(Collection<?> coll)` | (Violation) 지정된 컬렉션에 없는 Bag의 구성원은 카디널리티에 따라 모두 제거한다. |
| `int size()` | 모든 유형의 Bag에 있는 총 항목 수를 반환한다. |
| `Set<E> uniqueSet()` | Bag에 있는 고유 요소 집합을 반환한다. |

### 3) Bag Interface 예제

* 예제

```java
import org.apache.commons.collections4.Bag;
import org.apache.commons.collections4.bag.HashBag;

public class BagTester {
    public static void main(String[] args) {
        Bag<String> bag = new HashBag<>();
        //add "a" two times to the bag.
        bag.add("a", 2);

        //add "b" one time to the bag.
        bag.add("b");

        //add "c" one time to the bag.
        bag.add("c");

        //add "d" three times to the bag.
        bag.add("d", 3);

        //get the count of "d" present in bag.
        System.out.println("d is present " + bag.getCount("d") + " times.");
        System.out.println("bag: " + bag);

        //get the set of unique values from the bag
        System.out.println("Unique Set: " + bag.uniqueSet());

        //remove 2 occurrences of "d" from the bag
        bag.remove("d", 2);
        System.out.println("2 occurences of d removed from bag: " + bag);
        System.out.println("d is present " + bag.getCount("d") + " times.");
        System.out.println("bag: " + bag);
        System.out.println("Unique Set: " + bag.uniqueSet());
    }
}
```

* 결과

```text
d is present 3 times.
bag: [2:a,1:b,1:c,3:d]
Unique Set: [a, b, c, d]
2 occurences of d removed from bag: [2:a,1:b,1:c,1:d]
d is present 1 times.
bag: [2:a,1:b,1:c,1:d]
Unique Set: [a, b, c, d]
```

## [출처 및 참고]
* <https://www.tutorialspoint.com/commons_collections/commons_collections_bag.htm>
