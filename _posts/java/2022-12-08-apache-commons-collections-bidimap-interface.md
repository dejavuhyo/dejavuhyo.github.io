---
title: BidiMap Interface
author: dejavuhyo
date: 2022-12-08 21:20:00 +0900
categories: [Language, Java]
tags: [apache-commons-collections, bag-interface, bidimap, apache-commons, apache-collections, apache-interface, commons-interface]
---

## 1. BidiMap Interface (Apache Commons Collections)
양방향 맵을 사용하면 값을 사용하여 키를 조회할 수 있고 키를 사용하여 쉽게 값을 조회할 수 있다.

### 1) 선언
`org.apache.commons.collections4.BidiMap<K,V>`

```java
public interface BidiMap<K,V> extends IterableMap<K,V>
```

### 2) Methods

| Method | Description |
|:-----:|:-----:|
| `K getKey(Object value)` | 지정한 값에 현재 매핑된 키를 가져온다. |
| `BidiMap<V,K> inverseBidiMap()` | 키와 값이 반대인 이 맵의 뷰를 가져온다. |
| `V put(K key, V value)` | 키-값 쌍을 맵에 넣어 이전 쌍을 바꾼다. |
| `K removeValue(Object value)` | 현재 지정된 값에 매핑된 키-값 쌍을 제거한다(옵션 작업). |
| `Set<V> values()` | 이 맵에 포함된 값의 설정 보기를 반환한다. |

### 3) BidiMap Interface 예제

* 예제

```java
import org.apache.commons.collections4.BidiMap;
import org.apache.commons.collections4.bidimap.TreeBidiMap;

public class BidiMapTester {
    public static void main(String[] args) {
        BidiMap<String, String> bidi = new TreeBidiMap<>();

        bidi.put("One", "1");
        bidi.put("Two", "2");
        bidi.put("Three", "3");

        System.out.println(bidi.get("One"));
        System.out.println(bidi.getKey("1"));
        System.out.println("Original Map: " + bidi);

        bidi.removeValue("1");
        System.out.println("Modified Map: " + bidi);
        BidiMap<String, String> inversedMap = bidi.inverseBidiMap();
        System.out.println("Inversed Map: " + inversedMap);
    }
}
```

* 결과

```text
1
One
Original Map: {One=1, Three=3, Two=2}
Modified Map: {Three=3, Two=2}
Inversed Map: {2=Two, 3=Three}
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/commons_collections/commons_collections_bidimap.htm](https://www.tutorialspoint.com/commons_collections/commons_collections_bidimap.htm)
