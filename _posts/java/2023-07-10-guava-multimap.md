---
title: Guava Multimap
author: dejavuhyo
date: 2023-07-10 08:40:00 +0900
categories: [Language, Java]
tags: [guava-multimap, guava, multimap, 구아바-멀티맵, 멀티맵, 구아바]
---

## 1. 메이븐 종속성
종속성을 추가한다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.0.1-jre</version>
</dependency>
```

최신 버전은 [여기](https://central.sonatype.com/artifact/org.netbeans.external/com-google-guava)에서 찾을 수 있다.

## 2. 멀티맵 구현
Guava Multimap의 경우 동일한 키에 대해 두 개의 값을 추가하면 두 번째 값이 첫 번째 값을 무시하지 않는다. 대신 결과 맵에 두 개의 값이 있다.

```java
String key = "a-key";
Multimap<String, String> map = ArrayListMultimap.create();

map.put(key, "firstValue");
map.put(key, "secondValue");

assertEquals(2, map.size());
```

Map의 내용을 출력하면 다음이 출력된다.

```text
{a-key=[firstValue, secondValue]}
```

"a-key" 키로 값을 가져올 때 "firstValue" 및 "secondValue"를 결과로 포함하는 Collection<String>을 얻는다.

```java
Collection<String> values = map.get(key);
```

출력 값은 다음과 같이 출력된다.

```text
[firstValue, secondValue]
```

## 3. 표준 Map과 비교
`java.util` 패키지의 표준 맵은 동일한 키에 여러 값을 할당하는 기능을 제공하지 않는다. 동일한 키를 사용하여 맵에 두 개의 값을 `put()`하는 간단한 경우이다.

```java
String key = "a-key";
Map<String, String> map = new LinkedHashMap<>();

map.put(key, "firstValue");
map.put(key, "secondValue");

assertEquals(1, map.size());
```

결과 맵에는 첫 번째 값을 재정의하는 두 번째 `put()` 작업 때문에 하나의 요소("secondValue")만 있다. Guava의 Multimap과 동일한 동작을 수행하려면 값 유형으로 `List<String>`이 ​​있는 맵을 만들어야 한다.

```java
String key = "a-key";
Map<String, List<String>> map = new LinkedHashMap<>();

List<String> values = map.get(key);
if(values == null) {
    values = new LinkedList<>();
    values.add("firstValue");
    values.add("secondValue");
 }

map.put(key, values);

assertEquals(1, map.size());
```

분명히 사용하기가 그리 편리하지 않다. 그리고 코드에 이러한 요구 사항이 있는 경우 Guava의 Multimap이 `java.util.Map` 보다 더 나은 선택이 될 수 있다.

여기서 한 가지 주의할 점은 두 개의 요소가 포함된 목록이 있지만 `size()` 메서드는 1을 반환한다. 멀티맵에서 `size()`는 맵에 저장된 실제 값 수를 반환하지만 `keySet().size()`는 고유 키 수를 반환한다.

## 4. 멀티맵의 장점
멀티맵은 일반적으로 `Map<K, Collection<V>>`가 나타날 수 있는 위치에서 사용된다. 차이점은 다음과 같다.

* `put()`으로 항목을 추가하기 전에 빈 컬렉션을 채울 필요가 없다.

* `get()` 메서드는 null을 반환하지 않고 빈 컬렉션만 반환한다(`Map<String, Collection<V>>`와 같이 null에 대해 확인할 필요가 없음).

* 키는 하나 이상의 값에 매핑되는 경우에만 Multimap에 포함된다. 키에 연결된 값이 0이 되도록 하는 모든 작업은 Multimap에서 해당 키를 제거하는 효과가 있다(`Map<String, Collection<V>>`에서 컬렉션에서 모든 값을 제거하더라도 여전히 빈 컬렉션을 유지한다. 불필요한 메모리 오버헤드).

* 총 항목 값 개수는 `size()`로 사용할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/guava-multimap](https://www.baeldung.com/guava-multimap)
