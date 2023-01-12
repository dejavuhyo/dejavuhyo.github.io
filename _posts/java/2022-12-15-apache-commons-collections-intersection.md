---
title: Intersection
author: dejavuhyo
date: 2022-12-15 21:00:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, intersection, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. 교차로 확인 (Apache Commons Collections)
CollectionUtils의 `intersection()` 메서드를 사용하여 두 컬렉션 간의 공통 개체를 가져올 수 있다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.intersection()`

```java
public static <O> Collection<O> intersection(Iterable<? extends O> a, Iterable<? extends O> b)
```

### 2) Parameters

* a: 첫 번째(하위) 컬렉션은 null일 수 없다.

* b: 두 번째(슈퍼) 컬렉션은 null일 수 없다.

### 3) Return Value
두 컬렉션의 교차점이다.

### 4) Intersection 예제

* 예제

```java
import org.apache.commons.collections4.CollectionUtils;

import java.util.Arrays;
import java.util.List;

public class CollectionUtilsTester {
    public static void main(String[] args) {
        //checking inclusion
        List<String> list1 = Arrays.asList("A", "A", "A", "C", "B", "B");
        List<String> list2 = Arrays.asList("A", "A", "B", "B");
        System.out.println("List 1: " + list1);
        System.out.println("List 2: " + list2);
        System.out.println("Commons Objects of List 1 and List 2: " + CollectionUtils.intersection(list1, list2));
    }
}
```

* 결과

```text
List 1: [A, A, A, C, B, B]
List 2: [A, A, B, B]
Commons Objects of List 1 and List 2: [A, A, B, B]
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_intersection.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_intersection.htm)
