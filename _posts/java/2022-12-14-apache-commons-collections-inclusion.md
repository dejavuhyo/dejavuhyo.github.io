---
title: Inclusion
author: dejavuhyo
date: 2022-12-14 20:30:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, inclusion, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. 하위 목록 확인 (Apache Commons Collections)
CollectionUtils의 `isSubCollection()` 메서드를 사용하여 컬렉션에 지정된 컬렉션이 포함되어 있는지 확인할 수 있다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.isSubCollection()`

```java
public static boolean isSubCollection(Collection<?> a, Collection<?> b)
```

### 2) Parameters

* a: 첫 번째(하위) 컬렉션은 null일 수 없다.

* b: 두 번째(슈퍼) 컬렉션은 null일 수 없다.

### 3) Return Value
a가 b의 하위 집합인 경우에만 참이다.

### 4) Inclusion 예제

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
        System.out.println("Is List 2 contained in List 1: " + CollectionUtils.isSubCollection(list2, list1));
    }
}
```

* 결과

```text
List 1: [A, A, A, C, B, B]
List 2: [A, A, B, B]
Is List 2 contained in List 1: true
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_inclusion.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_inclusion.htm)
