---
title: Guava 컬렉션 결합 및 분할
author: dejavuhyo
date: 2023-07-11 08:35:00 +0900
categories: [Language, Java]
tags: [guava-collections, guava, collections-joiner, collections-splitter, 구아바, 구아바-컬렉션, 컬렉션-결합, 컬렉션-분할]
---

## 1. Joiner를 사용하여 List를 String으로 변환
Joiner를 사용하여 List를 String에 조인하는 간단한 예제이다. 다음 예제에서는 쉼표 ","를 구분 기호로 사용하여 이름 List를 하나의 String으로 결합한다.

```java
@Test
public void whenConvertListToString_thenConverted() {
    List<String> names = Lists.newArrayList("John", "Jane", "Adam", "Tom");
    String result = Joiner.on(",").join(names);

    assertEquals(result, "John,Jane,Adam,Tom");
}
```

## 2. Joiner를 사용하여 Map을 String으로 변환
Joiner를 사용하여 Map을 String으로 변환하는 방법이다. 다음 예제에서는 `withKeyValueSeparator()`를 사용하여 키와 해당 값을 조인한다.

```java
@Test
public void whenConvertMapToString_thenConverted() {
    Map<String, Integer> salary = Maps.newHashMap();
    salary.put("John", 1000);
    salary.put("Jane", 1500);
    String result = Joiner.on(" , ").withKeyValueSeparator(" = ").join(salary);

    assertThat(result, containsString("John = 1000"));
    assertThat(result, containsString("Jane = 1500"));
}
```

## 3. 중첩된 컬렉션 Join
중첩 컬렉션을 String에 조인하는 방법이다. 다음 예제에서는 각 List를 String으로 변환한 결과를 조인한다.

```java
@Test
public void whenJoinNestedCollections_thenJoined() {
    List<ArrayList<String>> nested = Lists.newArrayList(
      Lists.newArrayList("apple", "banana", "orange"),
      Lists.newArrayList("cat", "dog", "bird"),
      Lists.newArrayList("John", "Jane", "Adam"));
    String result = Joiner.on(";").join(Iterables.transform(nested,new Function<List<String>, String>() {
          @Override
          public String apply(List<String> input) {
              return Joiner.on("-").join(input);
          }
      }));

    assertThat(result, containsString("apple-banana-orange"));
    assertThat(result, containsString("cat-dog-bird"));
    assertThat(result, containsString("John-Jane-Adam"));
}
```

## 4. Joiner를 사용하는 동안 Null 값 처리
Joiner를 사용하는 동안 Null 값을 처리하는 다양한 방법이다.

컬렉션에 사용하는 동안 null 값을 건너뛰려면 다음 예제와 같이 `skipNulls()`를 사용한다.

```java
@Test
public void whenConvertListToStringAndSkipNull_thenConverted() {
    List<String> names = Lists.newArrayList("John", null, "Jane", "Adam", "Tom");
    String result = Joiner.on(",").skipNulls().join(names);

    assertEquals(result, "John,Jane,Adam,Tom");
}
```

null 값을 건너뛰고 싶지 않고 대신 바꾸려면 `useForNull()`을 사용한다.

```java
@Test
public void whenUseForNull_thenUsed() {
    List<String> names = Lists.newArrayList("John", null, "Jane", "Adam", "Tom");
    String result = Joiner.on(",").useForNull("nameless").join(names);

    assertEquals(result, "John,nameless,Jane,Adam,Tom");
}
```

`useForNull()`은 원래 목록을 변경하지 않고 조인 출력에만 영향을 준다.

## 5. Splitter를 사용하여 String에서 List 만들기
String을 List로 분할하는 방법이다. 다음 예제에서는 "-" 구분 기호를 사용하여 입력 String을 List로 분할한다.

```java
@Test
public void whenCreateListFromString_thenCreated() {
    String input = "apple - banana - orange";
    List<String> result = Splitter.on("-").trimResults().splitToList(input);

    assertThat(result, contains("apple", "banana", "orange"));
}
```

`trimResults()`는 결과 하위 문자열에서 선행 및 후행 공백을 제거한다.

## 6. Splitter를 사용하여 String에서 Map 생성
Splitter를 사용하여 문자열에서 맵을 만드는 방법이다. 다음 예제에서는 `withKeyValueSeparator()`를 사용하여 String을 Map으로 분할한다.

```java
@Test
public void whenCreateMapFromString_thenCreated() {
    String input = "John=first,Adam=second";
    Map<String, String> result = Splitter.on(",").withKeyValueSeparator("=").split(input);

    assertEquals("first", result.get("John"));
    assertEquals("second", result.get("Adam"));
}
```

## 7. 여러 구분 기호로 String 분할
여러 구분 기호로 문자열을 분할하는 방법이다. 다음 예에서는 "."을 모두 사용한다. 그리고 ","는 문자열을 분할한다.

```java
@Test
public void whenSplitStringOnMultipleSeparator_thenSplit() {
    String input = "apple.banana,,orange,,.";
    List<String> result = Splitter.onPattern("[.,]").omitEmptyStrings().splitToList(input);

    assertThat(result, contains("apple", "banana", "orange"));
}
```

`omitEmptyStrings()`는 빈 문자열을 무시하고 결과 List에 추가하지 않는다.

## 8. 특정 길이로 String 분할
문자열을 특정 길이로 분할하는 방법이다. 다음 예제에서는 문자열을 3자마다 분할한다.

```java
@Test
public void whenSplitStringOnSpecificLength_thenSplit() {
    String input = "Hello world";
    List<String> result = Splitter.fixedLength(3).splitToList(input);

    assertThat(result, contains("Hel", "lo ", "wor", "ld"));
}
```

## 9. 분할 결과 Limit
분할 결과를 Limit하는 방법이다. Splitter가 특정 항목 수 후에 분할을 중지하도록 하려면 다음과 같이 `limit()`를 사용한다.

```java
@Test
public void whenLimitSplitting_thenLimited() {
    String input = "a,b,c,d,e";
    List<String> result = Splitter.on(",")
                                  .limit(4)
                                  .splitToList(input);

    assertEquals(4, result.size());
    assertThat(result, contains("a", "b", "c", "d,e"));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/guava-joiner-and-splitter-tutorial](https://www.baeldung.com/guava-joiner-and-splitter-tutorial)
