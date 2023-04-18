---
title: Java 맵에 중복 키 저장
author: dejavuhyo
date: 2023-04-19 08:45:00 +0900
tags: [java-map-keys, map-duplicate-keys, duplicate-keys, 자바-맵-중복-키, 맵-중복-키, 중복-키]
---

## 1. 표준 Maps
Java에는 인터페이스 Map의 여러 구현이 있으며 각 구현에는 고유한 특성이 있다.

그러나 기존 Java 핵심 Map 구현 중 어떤 것도 Map이 단일 키에 대한 여러 값을 처리하도록 허용하지 않는다.

보시다시피 동일한 키에 대해 두 개의 값을 삽입하려고 하면 두 번째 값이 저장되고 첫 번째 값은 삭제된다.

또한 `put(K key, V value)` 메서드의 모든 적절한 구현에 의해 반환된다.

```java
Map<String, String> map = new HashMap<>();
assertThat(map.put("key1", "value1")).isEqualTo(null);
assertThat(map.put("key1", "value2")).isEqualTo("value1");
assertThat(map.get("key1")).isEqualTo("value2");
```

## 2. Collection 가치
당연히 Map의 모든 값에 대해 Collection을 사용하면 작업을 수행할 수 있다.

```java
Map<String, List<String>> map = new HashMap<>();
List<String> list = new ArrayList<>();
map.put("key1", list);
map.get("key1").add("value1");
map.get("key1").add("value2");

assertThat(map.get("key1").get(0)).isEqualTo("value1");
assertThat(map.get("key1").get(1)).isEqualTo("value2");
```

그러나 이 솔루션에는 여러 가지 단점이 있으며 오류가 발생하기 쉽다. 이는 모든 값에 대해 Collection을 인스턴스화하고, 값을 추가하거나 제거하기 전에 Collection의 존재를 확인하고, 값이 남아 있지 않으면 수동으로 삭제해야 함을 의미한다.

Java 8부터 `compute()` 메서드를 활용하고 개선할 수 있다.

```java
Map<String, List<String>> map = new HashMap<>();
map.computeIfAbsent("key1", k -> new ArrayList<>()).add("value1");
map.computeIfAbsent("key1", k -> new ArrayList<>()).add("value2");

assertThat(map.get("key1").get(0)).isEqualTo("value1");
assertThat(map.get("key1").get(1)).isEqualTo("value2");
```

그러나 타사 라이브러리를 사용하지 못하도록 하는 제한적인 회사 정책과 같이 피해야 할 타당한 이유가 없는 한 피해야 한다.

그러나 타사 라이브러리를 사용할 수 없도록 제한적인 회사 정책과 같은 매우 타당한 이유가 없는 한 피해야 한다.

## 3. Apache Commons Collections
여느 때처럼 아파치는 문제에 대한 해결책을 가지고 있다.

먼저 Common Collections의 최신 릴리스를 가져온다.

```xml
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-collections4</artifactId>
  <version>4.1</version>
</dependency>
```

### 1) MultiMap
`org.apache.commons.collections4.MultiMap` 인터페이스는 각 키에 대한 값 모음을 보유하는 Map을 정의한다.

`org.apache.commons.collections4.map`에 의해 구현된다. 실제로 안에서의 동작은 대부분의 상용구를 자동으로 처리하는 MultiValueMap 클래스이다.

```java
MultiMap<String, String> map = new MultiValueMap<>();
map.put("key1", "value1");
map.put("key1", "value2");
assertThat((Collection<String>) map.get("key1"))
  .contains("value1", "value2");
```

이 클래스는 Common Collections 3.2 부터 사용할 수 있지만 스레드로부터 안전하지 않으며 Common Collections 4.1에서 더 이상 사용되지 않는다. 최신 버전으로 업그레이드할 수 없을 때만 사용해야 한다.

### 2) MultiValuedMap
MultiMap의 후속 버전은 `org.apache.common.collections4`이다. MultiValueMap 인터페이스이다. 여러 개의 구현을 사용할 수 있다.

중복 항목을 유지하는 ArrayList에 여러 값을 저장하는 방법이다.

```java
MultiValuedMap<String, String> map = new ArrayListValuedHashMap<>();
map.put("key1", "value1");
map.put("key1", "value2");
map.put("key1", "value2");
assertThat((Collection<String>) map.get("key1"))
  .containsExactly("value1", "value2", "value2");
```

또는 중복 항목을 삭제하는 HashSet을 사용할 수 있다.

```java
MultiValuedMap<String, String> map = new HashSetValuedHashMap<>();
map.put("key1", "value1");
map.put("key1", "value1");
assertThat((Collection<String>) map.get("key1"))
  .containsExactly("value1");
```

위의 구현은 모두 스레드로부터 안전하지 않다.

UnmodifiableMultiValuedMap 데코레이터를 사용하여 불변으로 만드는 방법이다.

```java
@Test(expected = UnsupportedOperationException.class)
public void givenUnmodifiableMultiValuedMap_whenInserting_thenThrowingException() {
    MultiValuedMap<String, String> map = new ArrayListValuedHashMap<>();
    map.put("key1", "value1");
    map.put("key1", "value2");
    MultiValuedMap<String, String> immutableMap =
      MultiMapUtils.unmodifiableMultiValuedMap(map);
    immutableMap.put("key1", "value3");
}
```

## 4. Guava Multimap
Guava는 Java API용 Google Core 라이브러리이다.

프로젝트에서 구아바를 가져온다.

```xml
<dependency>
  <groupId>com.google.guava</groupId>
  <artifactId>guava</artifactId>
  <version>31.0.1-jre</version>
</dependency>
```

Guava는 처음부터 여러 구현 경로를 따랐다.

가장 일반적인 것은 `com.google.common.collect`이다. 모든 값에 대해 ArrayList가 지원하는 HashMap을 사용하는 ArrayListMultimap이다.

```java
Multimap<String, String> map = ArrayListMultimap.create();
map.put("key1", "value2");
map.put("key1", "value1");
assertThat((Collection<String>) map.get("key1"))
  .containsExactly("value2", "value1");
```

항상 그렇듯이, Multimap 인터페이스의 불변 구현인 `com.google.common.collect`를 선호해야 한다. `ImmutableListMultimap` 및 `com.google.common.collect.ImmutableSetMultimap` 이다.

### 1) 일반적인 맵 구현
특정 Map 구현이 필요할 때 가장 먼저 해야 할 일은 Guava가 이미 구현했을 가능성이 있으므로 존재하는지 확인하는 것이다.

예를 들어 `com.google.common.collect`를 사용할 수 있다. 키와 값의 삽입 순서를 유지하는 LinkedHashMultimap이다.

```java
Multimap<String, String> map = LinkedHashMultimap.create();
map.put("key1", "value3");
map.put("key1", "value1");
map.put("key1", "value2");
assertThat((Collection<String>) map.get("key1"))
  .containsExactly("value3", "value1", "value2");
```

또는 `com.google.common.collect`를 사용할 수 있다. 키와 값을 자연스러운 순서로 반복하는 TreeMultimap이다.

```java
Multimap<String, String> map = TreeMultimap.create();
map.put("key1", "value3");
map.put("key1", "value1");
map.put("key1", "value2");
assertThat((Collection<String>) map.get("key1"))
  .containsExactly("value1", "value2", "value3");
```

### 2) 사용자 정의 MultiMap 위조
다른 많은 구현을 사용할 수 있다.

그러나 아직 구현되지 않은 Map 및 List를 사용하고 싶을 수 있다.

다행스럽게도 구아바에는 `Multimap.newMultimap()` 이라는 팩토리 메서드가 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-map-duplicate-keys](https://www.baeldung.com/java-map-duplicate-keys)
