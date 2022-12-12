---
title: Filtering Objects
author: dejavuhyo
date: 2022-12-12 19:20:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, filtering-objects, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. filter() method (Apache Commons Collections)
CollectionUtils의 `filter()` 메서드를 사용하여 전달된 술어에 의해 제공된 조건을 충족하지 않는 개체를 제거하기 위해 목록을 필터링할 수 있다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.filter()`

```java
public static <T> boolean filter(Iterable<T> collection, Predicate<? super T> predicate)
```

### 2) Parameters

* collection: 입력을 가져올 컬렉션은 null이 아닐 수 있다.

* predicate: 필터로 사용할 술어가 null일 수 있다.

### 3) Return Value
이 호출에 의해 컬렉션이 수정되면 true이고, 그렇지 않으면 false이다.

### 4) filter() 예제

* 예제

```java
import org.apache.commons.collections4.CollectionUtils;
import org.apache.commons.collections4.Predicate;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class CollectionUtilsTester {
    public static void main(String[] args) {
        List<Integer> integerList = new ArrayList<Integer>();
        integerList.addAll(Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8));
        System.out.println("Original List: " + integerList);
        CollectionUtils.filter(integerList, new Predicate<Integer>() {
            @Override
            public boolean evaluate(Integer input) {
                if (input.intValue() % 2 == 0) {
                    return true;
                }
                return false;
            }
        });
        System.out.println("Filtered List (Even numbers): " + integerList);
    }
}
```

* 결과

```text
Original List: [1, 2, 3, 4, 5, 6, 7, 8]
Filtered List (Even numbers): [2, 4, 6, 8]
```

## 2. filterInverse() method (Apache Commons Collections)
CollectionUtils의 `filterInverse()` 메서드를 사용하여 목록을 필터링하여 개체를 제거할 수 있으며, 이는 전달된 술어에 의해 제공된 조건을 충족한다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.filterInverse()`

```java
public static <T> boolean filterInverse(Iterable<T> collection, Predicate<? super T> predicate)
```

### 2) Parameters

* collection: 입력을 가져올 컬렉션은 null이 아닐 수 있다.

* predicate: 필터로 사용할 술어가 null일 수 있다.

### 3) Return Value
이 호출에 의해 컬렉션이 수정되면 true이고, 그렇지 않으면 false입니다.

### 4) filterInverse() 예제

* 예제

```java
import org.apache.commons.collections4.CollectionUtils;
import org.apache.commons.collections4.Predicate;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class CollectionUtilsTester {
    public static void main(String[] args) {
        List<Integer> integerList = new ArrayList<Integer>();
        integerList.addAll(Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8));
        System.out.println("Original List: " + integerList);
        CollectionUtils.filterInverse(integerList, new Predicate<Integer>() {
            @Override
            public boolean evaluate(Integer input) {
                if (input.intValue() % 2 == 0) {
                    return true;
                }
                return false;
            }
        });
        System.out.println("Filtered List (Odd numbers): " + integerList);
    }
}
```

* 결과

```text
Original List: [1, 2, 3, 4, 5, 6, 7, 8]
Filtered List (Odd numbers): [1, 3, 5, 7]
```

## [출처 및 참고]
* <https://www.tutorialspoint.com/commons_collections/commons_collections_filtering_objects.htm>
