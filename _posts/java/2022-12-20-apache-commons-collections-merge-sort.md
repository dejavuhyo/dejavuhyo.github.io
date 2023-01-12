---
title: Merge & Sort
author: dejavuhyo
date: 2022-12-20 22:00:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, ignore-null, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. 정렬된 두 list 병합 (Apache Commons Collections)
CollectionUtils의 `collate()` 메서드를 사용하여 이미 정렬된 두 목록을 병합할 수 있다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.collate()`

```java
public static <O extends Comparable<? super O>> List<O> collate(Iterable<? extends O> a, Iterable<? extends O> b)
```

### 2) Parameters

* a: 첫 번째 컬렉션은 null일 수 없다.

* b: 두 번째 컬렉션은 null일 수 없다.

### 3) Return Value
컬렉션 a와 b의 요소를 포함하는 새로운 정렬된 List이다.

### 4) Exception

* NullPointerException: 컬렉션이 null인 경우

### 5) Merge & Sort 예제

* 예제

```java
import org.apache.commons.collections4.CollectionUtils;

import java.util.Arrays;
import java.util.List;

public class CollectionUtilsTester {
    public static void main(String[] args) {
        List<String> sortedList1 = Arrays.asList("A", "C", "E");
        List<String> sortedList2 = Arrays.asList("B", "D", "F");
        List<String> mergedList = CollectionUtils.collate(sortedList1, sortedList2);
        System.out.println(mergedList);
    }
}
```

* 결과

```text
[A, B, C, D, E, F]
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_merge_sort.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_merge_sort.htm)
