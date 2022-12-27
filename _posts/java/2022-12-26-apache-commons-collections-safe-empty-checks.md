---
title: Safe Empty Checks
author: dejavuhyo
date: 2022-12-26 19:15:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, safe-empty-checks, empty-checks, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. 비어 있지 않은 목록 확인 (Apache Commons Collections)
CollectionUtils의 `isNotEmpty()` 메서드를 사용하여 null 목록을 걱정하지 않고 목록이 비어 있지 않은지 확인할 수 있다. 따라서 목록의 크기를 확인하기 전에 null 검사를 모든 곳에 배치할 필요는 없다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.isNotEmpty()`

```java
public static boolean isNotEmpty(Collection<?> coll)
```

### 2) Parameters

* coll: 확인할 컬렉션이 null일 수 있다.

### 3) Return Value
null이 아닌 경우 true이고 비어 있지 않은 경우 true이다.

### 4) isNotEmpty 예제
`org.apache.commons.collections4.CollectionUtils.isNotEmpty()`

* 예제

```java
import org.apache.commons.collections4.CollectionUtils;

import java.util.List;

public class CollectionUtilsTester {
    public static void main(String[] args) {
        List<String> list = getList();
        System.out.println("Non-Empty List Check: " + checkNotEmpty1(list));
        System.out.println("Non-Empty List Check: " + checkNotEmpty1(list));
    }

    static List<String> getList() {
        return null;
    }

    static boolean checkNotEmpty1(List<String> list) {
        return !(list == null || list.isEmpty());
    }

    static boolean checkNotEmpty2(List<String> list) {
        return CollectionUtils.isNotEmpty(list);
    }
}
```

* 결과

```text
Non-Empty List Check: false
Non-Empty List Check: false
```

## 2. 빈 목록 확인 (Apache Commons Collections)
CollectionUtils의 `isEmpty()` 메서드를 사용하여 null 목록을 걱정하지 않고 목록이 비어 있는지 확인할 수 있다. 따라서 목록의 크기를 확인하기 전에 null 검사를 모든 곳에 배치할 필요는 없다.

### 1) 선언

```java
public static boolean isEmpty(Collection<?> coll)
```

### 2) Parameters
coll: 확인할 컬렉션이 null일 수 있다.

### 3) Return Value
비어 있는 경우 True이고 null이다.

### 4) isEmpty 예제
`org.apache.commons.collections4.CollectionUtils.isEmpty()`

* 예제

```java
import org.apache.commons.collections4.CollectionUtils;

import java.util.List;

public class CollectionUtilsTester {
    public static void main(String[] args) {
        List<String> list = getList();
        System.out.println("Empty List Check: " + checkEmpty1(list));
        System.out.println("Empty List Check: " + checkEmpty1(list));
    }

    static List<String> getList() {
        return null;
    }

    static boolean checkEmpty1(List<String> list) {
        return (list == null || list.isEmpty());
    }

    static boolean checkEmpty2(List<String> list) {
        return CollectionUtils.isEmpty(list);
    }
}
```

* 결과

```text
Empty List Check: true
Empty List Check: true
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_safe_checks.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_safe_checks.htm)
