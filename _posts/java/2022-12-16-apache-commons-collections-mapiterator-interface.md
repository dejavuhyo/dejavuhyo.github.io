---
title: MapIterator Interface
author: dejavuhyo
date: 2022-12-16 21:00:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, mapiterator-interface, mapiterator, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. MapIterator Interface (Apache Commons Collections)
JDK 맵 인터페이스는 EntrySet 또는 KeySet 개체에서 수행할 반복으로, 반복하기가 매우 어렵다. MapIterator는 Map을 통한 간단한 반복을 제공한다.

### 1) MapIterator Interface 예제

* 예제

```java
import org.apache.commons.collections4.IterableMap;
import org.apache.commons.collections4.MapIterator;
import org.apache.commons.collections4.map.HashedMap;

public class MapIteratorTester {
    public static void main(String[] args) {
        IterableMap<String, String> map = new HashedMap<>();

        map.put("1", "One");
        map.put("2", "Two");
        map.put("3", "Three");
        map.put("4", "Four");
        map.put("5", "Five");

        MapIterator<String, String> iterator = map.mapIterator();
        while (iterator.hasNext()) {
            Object key = iterator.next();
            Object value = iterator.getValue();

            System.out.println("key: " + key);
            System.out.println("Value: " + value);

            iterator.setValue(value + "_");
        }
        System.out.println(map);
    }
}
```

* 결과

```text
key: 3
Value: Three
key: 5
Value: Five
key: 2
Value: Two
key: 4
Value: Four
key: 1
Value: One
{3=Three_, 5=Five_, 2=Two_, 4=Four_, 1=One_}
```

## [출처 및 참고]
* <https://www.tutorialspoint.com/commons_collections/commons_collections_mapiterator.htm>
