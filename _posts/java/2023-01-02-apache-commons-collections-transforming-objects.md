---
title: Transforming Objects
author: dejavuhyo
date: 2023-01-02 20:45:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, transforming-objects, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. 목록 변환 (Apache Commons Collections)
CollectUtils의 `collect()` 메서드는 한 유형의 개체 목록을 다른 유형의 개체 목록으로 변환하는데 사용할 수 있다.

### 1) 선언
`org.apache.commons.collections4.CollectionUtils.collect()`

```java
public static <I,O> Collection<O> collect(Iterable<I> inputCollection, Transformer<? super I,? extends O> transformer)
```

### 2) Parameters

* inputCollection: 입력을 가져올 컬렉션은 null이 아닐 수 있다.

* Transformer: 사용할 변환기가 null일 수 있다.

### 3) Return Value
변환된 결과(new list)이다.

### 4) Exception

* NullPointerException: 컬렉션이 null인 경우

### 5) Transforming Objects 예제

* 예제

```java
import org.apache.commons.collections4.CollectionUtils;
import org.apache.commons.collections4.Transformer;

import java.util.Arrays;
import java.util.List;

public class CollectionUtilsTester {
    public static void main(String[] args) {
        List<String> stringList = Arrays.asList("1", "2", "3");
        List<Integer> integerList = (List<Integer>) CollectionUtils.collect(stringList, new Transformer<String, Integer>() {
            @Override
            public Integer transform(String input) {
                return Integer.parseInt(input);
            }
        });
        System.out.println(integerList);
    }
}
```

* 결과

```text
[1, 2, 3]
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_transforming_objects.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_transforming_objects.htm)
