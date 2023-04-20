---
title: Java 두 개의 HashMap 비교
author: dejavuhyo
date: 2023-04-21 08:35:00 +0900
categories: [Language, Java]
tags: [java-compare-hashmaps, compare-hashmaps, 자바-해시맵-비교, 해시맵-비교]
---

## 1. Map.equals() 사용
`Map.equals()`를 사용하여 두 개의 HashMap에 동일한 항목이 있는지 확인한다.

```java
@Test
public void whenCompareTwoHashMapsUsingEquals_thenSuccess() {
    Map<String, String> asiaCapital1 = new HashMap<String, String>();
    asiaCapital1.put("Japan", "Tokyo");
    asiaCapital1.put("South Korea", "Seoul");

    Map<String, String> asiaCapital2 = new HashMap<String, String>();
    asiaCapital2.put("South Korea", "Seoul");
    asiaCapital2.put("Japan", "Tokyo");

    Map<String, String> asiaCapital3 = new HashMap<String, String>();
    asiaCapital3.put("Japan", "Tokyo");
    asiaCapital3.put("China", "Beijing");

    assertTrue(asiaCapital1.equals(asiaCapital2));
    assertFalse(asiaCapital1.equals(asiaCapital3));
}
```

여기서는 세 개의 HashMap 개체를 만들고 항목을 추가한다. 그런 다음 `Map.equals()`를 사용하여 두 개의 HashMap에 동일한 항목이 있는지 확인한다.

**`Map.equals()`가 작동하는 방식은 `Object.equals()` 메서드를 사용하여 키와 값을 비교하는 것이다.** 이것은 키와 값 객체가 모두 `equals()`를 적절하게 구현할 때만 작동한다는 것을 의미한다.

예를 들어 `Map.equals()`는 값 유형이 배열일 때 작동하지 않는다. 배열의 `equals()` 메서드는 배열의 내용이 아니라 ID를 비교하기 때문이다.

```java
@Test
public void whenCompareTwoHashMapsWithArrayValuesUsingEquals_thenFail() {
    Map<String, String[]> asiaCity1 = new HashMap<String, String[]>();
    asiaCity1.put("Japan", new String[] { "Tokyo", "Osaka" });
    asiaCity1.put("South Korea", new String[] { "Seoul", "Busan" });

    Map<String, String[]> asiaCity2 = new HashMap<String, String[]>();
    asiaCity2.put("South Korea", new String[] { "Seoul", "Busan" });
    asiaCity2.put("Japan", new String[] { "Tokyo", "Osaka" });

    assertFalse(asiaCity1.equals(asiaCity2));
}
```

## 2. 자바 스트림 API 사용
Java 8 Stream API를 사용하여 HashMaps를 비교하는 자체 메서드를 구현할 수도 있다.

```java
private boolean areEqual(Map<String, String> first, Map<String, String> second) {
    if (first.size() != second.size()) {
        return false;
    }

    return first.entrySet().stream()
      .allMatch(e -> e.getValue().equals(second.get(e.getKey())));
}
```

단순화를 위해 이제 `HashMap<String, String>` 개체를 비교하는 데 사용할 수 있는 `areEqual()` 메서드를 구현했다.

```java
@Test
public void whenCompareTwoHashMapsUsingStreamAPI_thenSuccess() {
    assertTrue(areEqual(asiaCapital1, asiaCapital2));
    assertFalse(areEqual(asiaCapital1, asiaCapital3));
}
```

그러나 `Arrays.equals()`를 사용하여 두 배열을 비교하여 배열 값을 처리하도록 고유한 메서드 `areEqualWithArrayValue()`를 사용자 지정할 수도 있다.

```java
private boolean areEqualWithArrayValue(Map<String, String[]> first, Map<String, String[]> second) {
    if (first.size() != second.size()) {
        return false;
    }

    return first.entrySet().stream()
      .allMatch(e -> Arrays.equals(e.getValue(), second.get(e.getKey())));
}
```

`Map.equals()`와 달리 메서드는 HashMaps를 배열 값과 성공적으로 비교한다.

```java
@Test
public void whenCompareTwoHashMapsWithArrayValuesUsingStreamAPI_thenSuccess() {
    assertTrue(areEqualWithArrayValue(asiaCity1, asiaCity2)); 
    assertFalse(areEqualWithArrayValue(asiaCity1, asiaCity3));
}
```

## 3. HashMap 키와 값 비교

### 1) HashMap 키 비교
`KeySet()`을 비교하여 두 개의 HashMap이 동일한 키를 가지고 있는지 확인할 수 있다.

```java
@Test
public void whenCompareTwoHashMapKeys_thenSuccess() {
    assertTrue(asiaCapital1.keySet().equals(asiaCapital2.keySet())); 
    assertFalse(asiaCapital1.keySet().equals(asiaCapital3.keySet()));
}
```

### 2) HashMap 값 비교
HashMap 값을 하나씩 비교하는 방법이다.

Stream API를 사용하여 두 HashMaps에서 동일한 값을 갖는 키를 확인하는 간단한 방법을 구현한다.

```java
private Map<String, Boolean> areEqualKeyValues(Map<String, String> first, Map<String, String> second) {
    return first.entrySet().stream()
      .collect(Collectors.toMap(e -> e.getKey(), 
        e -> e.getValue().equals(second.get(e.getKey()))));
}
```

`areEqualKeyValues()`를 사용하여 두 개의 다른 HashMap을 비교하여 어떤 키가 동일한 값을 가지고 있고 어떤 키가 다른 값을 가지고 있는지 자세히 볼 수 있다.

```java
@Test
public void whenCompareTwoHashMapKeyValuesUsingStreamAPI_thenSuccess() {
    Map<String, String> asiaCapital3 = new HashMap<String, String>();
    asiaCapital3.put("Japan", "Tokyo");
    asiaCapital3.put("South Korea", "Seoul");
    asiaCapital3.put("China", "Beijing");

    Map<String, String> asiaCapital4 = new HashMap<String, String>();
    asiaCapital4.put("South Korea", "Seoul");
    asiaCapital4.put("Japan", "Osaka");
    asiaCapital4.put("China", "Beijing");

    Map<String, Boolean> result = areEqualKeyValues(asiaCapital3, asiaCapital4);

    assertEquals(3, result.size());
    assertThat(result, hasEntry("Japan", false));
    assertThat(result, hasEntry("South Korea", true));
    assertThat(result, hasEntry("China", true));
}
```

## 4. Guava를 사용하여 차이점 매핑
마지막으로 Guava `Maps.difference()`를 사용하여 두 HashMaps 간의 자세한 차이를 얻는 방법이다.

이 메서드는 맵 간의 차이를 분석하는 데 유용한 여러 메서드가 있는 `MapDifference` 개체를 반환한다.

### 1) MapDifference.entriesDiffering()
MapDifference.entriesDiffering()을 사용하여 각 HashMap에서 다른 값을 갖는 공통 키를 얻는다.

```java
@Test
public void givenDifferentMaps_whenGetDiffUsingGuava_thenSuccess() {
    Map<String, String> asia1 = new HashMap<String, String>();
    asia1.put("Japan", "Tokyo");
    asia1.put("South Korea", "Seoul");
    asia1.put("India", "New Delhi");

    Map<String, String> asia2 = new HashMap<String, String>();
    asia2.put("Japan", "Tokyo");
    asia2.put("China", "Beijing");
    asia2.put("India", "Delhi");

    MapDifference<String, String> diff = Maps.difference(asia1, asia2);
    Map<String, ValueDifference<String>> entriesDiffering = diff.entriesDiffering();

    assertFalse(diff.areEqual());
    assertEquals(1, entriesDiffering.size());
    assertThat(entriesDiffering, hasKey("India"));
    assertEquals("New Delhi", entriesDiffering.get("India").leftValue());
    assertEquals("Delhi", entriesDiffering.get("India").rightValue());
}
```

`entryDiffering()` 메서드는 공통 키 집합과 ValueDifference 개체를 값 집합으로 포함하는 새 Map을 반환한다.

각 ValueDifference 개체에는 각각 두 맵의 값을 반환하는 `leftValue()` 및 `rightValue()` 메서드가 있다.

### 2) MapDifference.entriesOnlyOnRight() 및 MapDifference.entriesOnlyOnLeft()
그런 다음 `MapDifference.entriesOnlyOnRight()` 및 `MapDifference.entriesOnlyOnLeft()`를 사용하여 하나의 HashMap에만 존재하는 항목을 가져올 수 있다.

```java
@Test
public void givenDifferentMaps_whenGetEntriesOnOneSideUsingGuava_thenSuccess() {
    MapDifference<String, String> diff = Maps.difference(asia1, asia2);
    Map<String, String> entriesOnlyOnRight = diff.entriesOnlyOnRight();
    Map<String, String> entriesOnlyOnLeft = diff.entriesOnlyOnLeft();
    
    assertEquals(1, entriesOnlyOnRight.size());
    assertEquals(1, entriesOnlyOnLeft.size());
    assertThat(entriesOnlyOnRight, hasEntry("China", "Beijing"));
    assertThat(entriesOnlyOnLeft, hasEntry("South Korea", "Seoul"));
}
```

### 3) MapDifference.entriesInCommon()
다음으로 `MapDifference.entriesInCommon()`을 사용하여 공통 항목을 가져온다.

```java
@Test
public void givenDifferentMaps_whenGetCommonEntriesUsingGuava_thenSuccess() {
    MapDifference<String, String> diff = Maps.difference(asia1, asia2);
    Map<String, String> entriesInCommon = diff.entriesInCommon();

    assertEquals(1, entriesInCommon.size());
    assertThat(entriesInCommon, hasEntry("Japan", "Tokyo"));
}
```

### 4) Maps.difference() 동작 사용자 지정
`Maps.difference()`는 항목을 비교하기 위해 기본적으로 `equals()` 및 `hashCode()`를 사용하므로 항목을 제대로 구현하지 않는 객체에는 작동하지 않는다.

```java
@Test
public void givenSimilarMapsWithArrayValue_whenCompareUsingGuava_thenFail() {
    MapDifference<String, String[]> diff = Maps.difference(asiaCity1, asiaCity2);
    assertFalse(diff.areEqual());
}
```

그러나 Equivalence를 사용하여 비교에 사용되는 방법을 사용자 정의할 수 있다.

예를 들어, `String[]` 유형에 대한 Equivalence를 정의하여 원하는 대로 HashMaps의 `String[]` 값을 비교한다.

```java
@Test
public void givenSimilarMapsWithArrayValue_whenCompareUsingGuavaEquivalence_thenSuccess() {
    Equivalence<String[]> eq = new Equivalence<String[]>() {
        @Override
        protected boolean doEquivalent(String[] a, String[] b) {
            return Arrays.equals(a, b);
        }

        @Override
        protected int doHash(String[] value) {
            return value.hashCode();
        }
    };

    MapDifference<String, String[]> diff = Maps.difference(asiaCity1, asiaCity2, eq);
    assertTrue(diff.areEqual());

    diff = Maps.difference(asiaCity1, asiaCity3, eq); 
    assertFalse(diff.areEqual());
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-compare-hashmaps](https://www.baeldung.com/java-compare-hashmaps)
