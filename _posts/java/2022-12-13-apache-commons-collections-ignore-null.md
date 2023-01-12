---
title: Ignore Null
author: dejavuhyo
date: 2022-12-13 20:40:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, ignore-null, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. Null이 아닌 요소 확인 (Apache Commons Collections)
CollectionUtils의 `addIgnoreNull()` 메서드를 사용하여 Null이 아닌 값만 컬렉션에 추가되도록 할 수 있다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.addIgnoreNull()`

```java
public static <T> boolean addIgnoreNull(Collection<T> collection, T object)
```

### 2) Parameters

* collection: 추가할 컬렉션은 null일 수 없다.

* object: 추가할 개체이다. null인 경우 추가되지 않는다.

### 3) Return Value
컬렉션이 변경된 경우 True이다.

### 4) Exception

* NullPointerException: 컬렉션이 null인 경우

### 5) Ignore Null 예제

* 예제

```java
import org.apache.commons.collections4.CollectionUtils;

import java.util.LinkedList;
import java.util.List;

public class CollectionUtilsTester {
    public static void main(String[] args) {
        List<String> list = new LinkedList<String>();
        CollectionUtils.addIgnoreNull(list, null);
        CollectionUtils.addIgnoreNull(list, "a");

        System.out.println(list);

        if (list.contains(null)) {
            System.out.println("Null value is present");
        } else {
            System.out.println("Null value is not present");
        }
    }
}
```

* 결과

```text
[a]
Null value is not present
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_ignore_null.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_ignore_null.htm)
