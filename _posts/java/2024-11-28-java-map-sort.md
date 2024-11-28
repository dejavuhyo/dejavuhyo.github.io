---
title: Java Map을 Key, Value 기준 정렬
author: dejavuhyo
date: 2024-11-28 09:16:00 +0900
categories: [Language, Java]
tags: [java-map, map-sort, map-key-value, key-valu-sort, 자바-맵, 맵-정렬]
---

2024-11-28-java-map-sort.md

## 1. Key 기준 정렬
keySet을 이용하여 key 기준 정렬을 한다.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class MapTest {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("c", 3);
        map.put("b", 2);
        map.put("e", 5);
        map.put("a", 1);
        map.put("d", 4);

        List<String> keySetList = new ArrayList<>(map.keySet());

        Collections.sort(keySetList); // 오름차순
        for (String key : keySetList) {
            System.out.println(key + ":" + map.get(key));
        }

        Collections.reverse(keySetList); // 내림차순
        for (String key : keySetList) {
            System.out.println(key + ":" + map.get(key));
        }
    }
}
```

## 2. Value 기준 정렬
Comparator를 사용하여 Value 기준 정렬을 한다.

```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class MapTest {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("c", 4);
        map.put("b", 2);
        map.put("e", 3);
        map.put("a", 5);
        map.put("d", 1);

        List<String> keySetList = new ArrayList<>(map.keySet());

        // 오름차순
        keySetList.sort(new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return map.get(o1).compareTo(map.get(o2));
            }
        });

        for (String key : keySetList) {
            System.out.println(key + ":" + map.get(key));
        }

        // 내림차순
        keySetList.sort((o1, o2) -> map.get(o2).compareTo(map.get(o1)));
        for (String key : keySetList) {
            System.out.println(key + ":" + map.get(key));
        }
    }
}
```

## [출처 및 참고]
* [https://velog.io/@dev-easy/Java-Map%EC%9D%84-Key-Value%EB%A1%9C-%EC%A0%95%EB%A0%AC%ED%95%98%EA%B8%B0](https://velog.io/@dev-easy/Java-Map%EC%9D%84-Key-Value%EB%A1%9C-%EC%A0%95%EB%A0%AC%ED%95%98%EA%B8%B0)
