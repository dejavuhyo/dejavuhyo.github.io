---
title: Union
author: dejavuhyo
date: 2023-01-03 20:00:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, union, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. 결합 확인 (Apache Commons Collections)
CollectionUtils의 `union()` 메서드를 사용하여 두 컬렉션의 결합을 얻을 수 있다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.union()`

```java
public static <O> Collection<O> union(Iterable<? extends O> a, Iterable<? extends O> b)
```

### 2) Parameters

* a: 첫 번째 컬렉션은 null일 수 없다.

* b: 두 번째 컬렉션은 null일 수 없다.

### 3) Return Value
두 컬렉션의 결합이다.

### 4) Union 예제

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
        System.out.println("Union of List 1 and List 2: " + CollectionUtils.union(list1, list2));
    }
}
```

* 결과

```text
List 1: [A, A, A, C, B, B]
List 2: [A, A, B, B]
Union of List 1 and List 2: [A, A, A, B, B, C]
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_union.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_union.htm)
