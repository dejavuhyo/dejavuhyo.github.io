---
title: OrderedMap Interface
author: dejavuhyo
date: 2022-12-21 21:50:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, orderedmap-interface, orderedmap, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. OrderedMap Interface (Apache Commons Collections)
OrderedMap은 맵이 요소를 추가하는 순서를 유지하기 위한 새로운 인터페이스이다. 이 인터페이스는 Map의 반복자를 지원하며 Map에서 앞으로 또는 뒤로 양방향으로 반복할 수 있다.

### 1) OrderedMap Interface 예제

* 예제

```java
import org.apache.commons.collections4.OrderedMap;
import org.apache.commons.collections4.map.LinkedMap;

public class OrderedMapTester {
    public static void main(String[] args) {
        OrderedMap<String, String> map = new LinkedMap<String, String>();
        map.put("One", "1");
        map.put("Two", "2");
        map.put("Three", "3");

        System.out.println(map.firstKey());
        System.out.println(map.nextKey("One"));
        System.out.println(map.nextKey("Two"));
    }
}
```

* 결과

```text
One
Two
Three
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_orderedmap.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_orderedmap.htm)
