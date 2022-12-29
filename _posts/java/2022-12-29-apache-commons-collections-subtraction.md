---
title: Subtraction
author: dejavuhyo
date: 2022-12-29 18:20:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, subtraction, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. 감산 확인 (Apache Commons Collections)
CollectionUtils의 `subtract()` 메서드를 사용하여 한 컬렉션의 개체를 다른 컬렉션에서 빼서 새 컬렉션을 가져올 수 있다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.subtract()`

```java
public static <O> Collection<O> subtract(Iterable<? extends O> a, Iterable<? extends O> b)
```

### 2) Parameters

* a: 뺄 집합은 null일 수 없다.

* b: 뺄 집합은 null일 수 없다.

### 3) Return Value
결과가 포함된 새 컬렉션이다.

### 4) Subtraction 예제

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
        System.out.println("List 1 - List 2: " + CollectionUtils.subtract(list1, list2));
    }
}
```

* 결과

```text
List 1: [A, A, A, C, B, B]
List 2: [A, A, B, B]
List 1 - List 2: [A, C]
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_subtraction.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_subtraction.htm)
