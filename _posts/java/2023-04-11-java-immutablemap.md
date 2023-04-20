---
title: Java ImmutableMap
author: dejavuhyo
date: 2023-04-11 09:30:00 +0900
categories: [Language, Java]
tags: [java-immutablemap, immutablemap, 자바-불변맵, 불변맵]
---

## 1. Unmodifiable vs Immutable
Unmodifiable Map은 modifiable Map에 대한 래퍼일 뿐이며 수정을 직접 허용하지 않는다.

```java
Map<String, String> mutableMap = new HashMap<>();
mutableMap.put("USA", "North America");

Map<String, String> unmodifiableMap = Collections.unmodifiableMap(mutableMap);
assertThrows(UnsupportedOperationException.class,
  () -> unmodifiableMap.put("Canada", "North America"));
```

그러나 기본 mutable Map은 여전히 변경될 수 있으며 수정 사항은 Unmodifiable Map에도 반영된다.

```java
mutableMap.remove("USA");
assertFalse(unmodifiableMap.containsKey("USA"));
		
mutableMap.put("Mexico", "North America");
assertTrue(unmodifiableMap.containsKey("Mexico"));
```

반면에 Immutable Map은 자체 개인 데이터를 포함하며 수정을 허용하지 않는다. 따라서 Immutable Map의 인스턴스가 생성되면 데이터는 어떤 식으로든 변경할 수 없다.

## 2. 구아바의 불변 Map
Guava는 각 java.util의 변경 불가능한 버전을 제공한다. ImmutableMap을 사용하여 매핑한다. 수정하려고 할 때마다 UnsupportedOperationException이 발생한다.

자체 비공개 데이터가 포함되어 있으므로 이 데이터는 원본 지도가 변경되어도 변경되지 않는다.

ImmutableMap의 인스턴스를 만드는 다양한 방법이다.

### 1) copyOf() 메서드 사용
원본 맵에서와 같이 모든 항목의 복사본을 반환하는 `ImmutableMap.copyOf()` 메서드를 사용한다.

```java
ImmutableMap<String, String> immutableMap = ImmutableMap.copyOf(mutableMap);
assertTrue(immutableMap.containsKey("USA"));
```

직접 또는 간접적으로 수정할 수 없다.

```java
assertThrows(UnsupportedOperationException.class,
  () -> immutableMap.put("Canada", "North America"));

mutableMap.remove("USA");
assertTrue(immutableMap.containsKey("USA"));

mutableMap.put("Mexico", "North America");
assertFalse(immutableMap.containsKey("Mexico"));
```

### 2) builder() 메서드 사용
`mmutableMap.builder()` 메서드를 사용하여 원본 Map에서와 같이 모든 항목의 복사본을 만들 수 있다.

또한 이 방법을 사용하여 원본 Map에 없는 추가 항목을 추가할 수 있다.

```java
ImmutableMap<String, String> immutableMap = ImmutableMap.<String, String>builder()
  .putAll(mutableMap)
  .put("Costa Rica", "North America")
  .build();
assertTrue(immutableMap.containsKey("USA"));
assertTrue(immutableMap.containsKey("Costa Rica"));
```

이전 예와 동일하게 직접 또는 간접적으로 수정할 수 없다.

```java
assertThrows(UnsupportedOperationException.class,
  () -> immutableMap.put("Canada", "North America"));

mutableMap.remove("USA");
assertTrue(immutableMap.containsKey("USA"));

mutableMap.put("Mexico", "North America");
assertFalse(immutableMap.containsKey("Mexico"));
```

### 3) of() 메서드 사용
`ImmutableMap.of()` 메서드를 사용하여 즉석에서 제공되는 일련의 항목으로 불변 Map을 만들 수 있다. 최대 5개의 key/value 쌍을 지원한다.

```java
ImmutableMap<String, String> immutableMap
  = ImmutableMap.of("USA", "North America", "Costa Rica", "North America");
assertTrue(immutableMap.containsKey("USA"));
assertTrue(immutableMap.containsKey("Costa Rica"));
```

또한 수정할 수 없다.

```java
assertThrows(UnsupportedOperationException.class,
  () -> immutableMap.put("Canada", "North America"));
```

## [출처 및 참고]
* [https://www.baeldung.com/java-immutable-maps](https://www.baeldung.com/java-immutable-maps)
