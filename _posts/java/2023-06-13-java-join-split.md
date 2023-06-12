---
title: Java 배열 및 컬렉션 결합 및 분할
author: dejavuhyo
date: 2023-06-13 08:50:00 +0900
categories: [Language, Java]
tags: [java-join-split, java-array, java-collection, java-join, java-split, 자바-결합-분할, 자바-결합, 자바-분할]
---

## 1. 두 Array 결합
`Stream.concat`을 사용하여 두 배열을 결합한다.

```java
@Test
public void whenJoiningTwoArrays_thenJoined() {
    String[] animals1 = new String[] { "Dog", "Cat" };
    String[] animals2 = new String[] { "Bird", "Cow" };
    
    String[] result = Stream.concat(
      Arrays.stream(animals1), Arrays.stream(animals2)).toArray(String[]::new);

    assertArrayEquals(result, new String[] { "Dog", "Cat", "Bird", "Cow" });
}
```

## 2. 두 개의 컬렉션 조인
두 개의 컬렉션으로 동일한 조인을 수행한다.

```java
@Test
public void whenJoiningTwoCollections_thenJoined() {
    Collection<String> collection1 = Arrays.asList("Dog", "Cat");
    Collection<String> collection2 = Arrays.asList("Bird", "Cow", "Moose");
    
    Collection<String> result = Stream.concat(
      collection1.stream(), collection2.stream())
      .collect(Collectors.toList());

    assertTrue(result.equals(Arrays.asList("Dog", "Cat", "Bird", "Cow", "Moose")));
}
```

## 3. 필터로 두 개의 컬렉션 결합
10보다 큰 것을 필터링하는 두 개의 숫자 모음을 결합한다.

```java
@Test
public void whenJoiningTwoCollectionsWithFilter_thenJoined() {
    Collection<String> collection1 = Arrays.asList("Dog", "Cat");
    Collection<String> collection2 = Arrays.asList("Bird", "Cow", "Moose");
    
    Collection<String> result = Stream.concat(
      collection1.stream(), collection2.stream())
      .filter(e -> e.length() == 3)
      .collect(Collectors.toList());

    assertTrue(result.equals(Arrays.asList("Dog", "Cat", "Cow")));
}
```

## 4. 배열을 문자열로 결합
Collector를 사용하여 배열을 문자열로 결합한다.

```java
@Test
public void whenConvertArrayToString_thenConverted() {
    String[] animals = new String[] { "Dog", "Cat", "Bird", "Cow" };
    String result = Arrays.stream(animals).collect(Collectors.joining(", "));

    assertEquals(result, "Dog, Cat, Bird, Cow");
}
```

## 5. 컬렉션을 문자열로 결합
Collection을 사용하여 동일하게 한다.

```java
@Test
public void whenConvertCollectionToString_thenConverted() {
    Collection<String> animals = Arrays.asList("Dog", "Cat", "Bird", "Cow");
    String result = animals.stream().collect(Collectors.joining(", "));

    assertEquals(result, "Dog, Cat, Bird, Cow");
}
```

## 6. 맵을 문자열로 결합
Map에서 String을 만든다.

프로세스는 이전 예제와 매우 유사하지만 각 맵 항목을 먼저 조인하는 추가 단계가 있다.

```java
@Test
public void whenConvertMapToString_thenConverted() {
    Map<Integer, String> animals = new HashMap<>();
    animals.put(1, "Dog");
    animals.put(2, "Cat");
    animals.put(3, "Cow");

    String result = animals.entrySet().stream()
      .map(entry -> entry.getKey() + " = " + entry.getValue())
      .collect(Collectors.joining(", "));

    assertEquals(result, "1 = Dog, 2 = Cat, 3 = Cow");
}
```

## 7. 중첩 컬렉션을 문자열로 조인
일부 중첩된 컬렉션을 String에 결합한다.

다음 예제에서는 먼저 중첩된 각 컬렉션 내에서 조인한 다음 각각의 결과를 조인한다.

```java
@Test
public void whenConvertNestedCollectionToString_thenConverted() {
    Collection<List<String>> nested = new ArrayList<>();
    nested.add(Arrays.asList("Dog", "Cat"));
    nested.add(Arrays.asList("Cow", "Pig"));

    String result = nested.stream().map(
      nextList -> nextList.stream()
        .collect(Collectors.joining("-")))
      .collect(Collectors.joining("; "));

    assertEquals(result, "Dog-Cat; Cow-Pig");
}
```

## 8. 조인 시 Null 값 처리
필터를 사용하여 null 값을 건너뛰는 방법이다.

```java
@Test
public void whenConvertCollectionToStringAndSkipNull_thenConverted() {
    Collection<String> animals = Arrays.asList("Dog", "Cat", null, "Moose");
    String result = animals.stream()
      .filter(Objects::nonNull)
      .collect(Collectors.joining(", "));

    assertEquals(result, "Dog, Cat, Moose");
}
```

## 9. 컬렉션을 둘로 나누기
숫자 Collection을 중간에 있는 두 개의 Collection으로 분할한다.

```java
@Test
public void whenSplitCollectionHalf_thenConverted() {
    Collection<String> animals = Arrays.asList(
        "Dog", "Cat", "Cow", "Bird", "Moose", "Pig");
    Collection<String> result1 = new ArrayList<>();
    Collection<String> result2 = new ArrayList<>();
    AtomicInteger count = new AtomicInteger();
    int midpoint = Math.round(animals.size() / 2);

    animals.forEach(next -> {
        int index = count.getAndIncrement();
        if (index < midpoint) {
            result1.add(next);
        } else {
            result2.add(next);
        }
    });

    assertTrue(result1.equals(Arrays.asList("Dog", "Cat", "Cow")));
    assertTrue(result2.equals(Arrays.asList("Bird", "Moose", "Pig")));
}
```

## 10 .단어 길이로 배열 분할
배열을 단어 길이로 분할한다.

```java
@Test
public void whenSplitArrayByWordLength_thenConverted() {
    String[] animals = new String[] { "Dog", "Cat", "Bird", "Cow", "Pig", "Moose"};
    Map<Integer, List<String>> result = Arrays.stream(animals)
      .collect(Collectors.groupingBy(String::length));

    assertTrue(result.get(3).equals(Arrays.asList("Dog", "Cat", "Cow", "Pig")));
    assertTrue(result.get(4).equals(Arrays.asList("Bird")));
    assertTrue(result.get(5).equals(Arrays.asList("Moose")));
}
```

## 11. 문자열을 배열로 분할
문자열을 배열로 분할한다.

```java
@Test
public void whenConvertStringToArray_thenConverted() {
    String animals = "Dog, Cat, Bird, Cow";
    String[] result = animals.split(", ");

    assertArrayEquals(result, new String[] { "Dog", "Cat", "Bird", "Cow" });
}
```

## 12. 문자열을 컬렉션으로 분할
이 예제는 이전 예제와 유사하며 Array에서 Collection으로 변환하는 추가 단계가 있다.

```java
@Test
public void whenConvertStringToCollection_thenConverted() {
    String animals = "Dog, Cat, Bird, Cow";
    Collection<String> result = Arrays.asList(animals.split(", "));

    assertTrue(result.equals(Arrays.asList("Dog", "Cat", "Bird", "Cow")));
}
```

## 13. 문자열을 맵으로 분할
String에서 Map을 만든다. 각 항목에 대해 한 번, 키와 값에 대해 마지막으로 문자열을 두 번 분할해야 한다.

```java
@Test
public void whenConvertStringToMap_thenConverted() {
    String animals = "1 = Dog, 2 = Cat, 3 = Bird";

    Map<Integer, String> result = Arrays.stream(
      animals.split(", ")).map(next -> next.split(" = "))
      .collect(Collectors.toMap(entry -> Integer.parseInt(entry[0]), entry -> entry[1]));

    assertEquals(result.get(1), "Dog");
    assertEquals(result.get(2), "Cat");
    assertEquals(result.get(3), "Bird");
}
```

## 14. 여러 구분 기호로 문자열 분할
정규식을 사용하여 여러 구분 기호가 있는 문자열을 분할하고 빈 결과도 제거한다.

```java
@Test
public void whenConvertCollectionToStringMultipleSeparators_thenConverted() {
    String animals = "Dog. , Cat, Bird. Cow";

    Collection<String> result = Arrays.stream(animals.split("[,|.]"))
      .map(String::trim)
      .filter(next -> !next.isEmpty())
      .collect(Collectors.toList());

    assertTrue(result.equals(Arrays.asList("Dog", "Cat", "Bird", "Cow")));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-join-and-split](https://www.baeldung.com/java-join-and-split)
