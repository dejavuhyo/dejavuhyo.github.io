---
title: Java 인덱스를 사용하여 스트림을 반복하는 방법
author: dejavuhyo
date: 2023-11-29 22:20:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-string, 자바-스트림, 스트림-문자열]
---

## 1. 일반 자바 사용
정수 범위를 사용하여 스트림을 탐색할 수 있으며 원본 요소가 인덱스로 액세스할 수 있는 배열이나 컬렉션에 있다는 이점도 있다.

인덱스를 반복하고 이 접근 방식을 보여주는 메서드를 구현해 본다.

간단히 문자열 배열을 얻고 인덱스된 요소만 선택하려고 한다.

```java
public List<String> getEvenIndexedStrings(String[] names) {
    List<String> evenIndexedNames = IntStream
      .range(0, names.length)
      .filter(i -> i % 2 == 0)
      .mapToObj(i -> names[i])
      .collect(Collectors.toList());
    return evenIndexedNames;
}
```

이제 구현을 테스트해 본다.

```java
@Test
public void whenCalled_thenReturnListOfEvenIndexedStrings() {
    String[] names = {"Afrim", "Bashkim", "Besim", "Lulzim", "Durim", "Shpetim"};
    List<String> expectedResult = Arrays.asList("Afrim", "Besim", "Durim");
    List<String> actualResult = StreamIndices.getEvenIndexedStrings(names);
    assertEquals(expectedResult, actualResult);
}
```

## 2. StreamUtils 사용
proton-pack 라이브러리에 있는 StreamUtils의 `zipWithIndex()` 메서드를 사용하여 인덱스를 반복하는 또 다른 방법을 사용할 수 있다.

먼저 `pom.xml`에 추가해야 한다.

```xml
<dependency>
    <groupId>com.codepoetics</groupId>
    <artifactId>protonpack</artifactId>
    <version>1.16</version>
</dependency>
```

코드를 살펴본다.

```java
public List<Indexed<String>> getEvenIndexedStrings(List<String> names) {
    List<Indexed<String>> list = StreamUtils
      .zipWithIndex(names.stream())
      .filter(i -> i.getIndex() % 2 == 0)
      .collect(Collectors.toList());
    return list;
}
```

다음은 이 메서드를 테스트하고 성공적으로 통과한다.

```java
@Test
public void whenCalled_thenReturnListOfEvenIndexedStrings() {
    List<String> names = Arrays.asList("Afrim", "Bashkim", "Besim", "Lulzim", "Durim", "Shpetim");
    List<Indexed<String>> expectedResult = Arrays.asList(
      Indexed.index(0, "Afrim"), 
      Indexed.index(2, "Besim"), 
      Indexed.index(4, "Durim"));
    List<Indexed<String>> actualResult = StreamIndices.getEvenIndexedStrings(names);
    assertEquals(expectedResult, actualResult);
}
```

## 3. StreamEx 사용하기
StreamEx 라이브러리에서 EntryStream 클래스의 `filterKeyValue()`를 사용하여 인덱스를 반복할 수도 있다. 먼저 `pom.xml`에 추가해야 한다.

```xml
<dependency>
    <groupId>one.util</groupId>
    <artifactId>streamex</artifactId>
    <version>0.8.1</version>
</dependency>
```

이전 예제를 사용하여 이 방법을 간단히 적용해 본다.

```java
public List<String> getEvenIndexedStringsVersionTwo(List<String> names) {
    return EntryStream.of(names)
      .filterKeyValue((index, name) -> index % 2 == 0)
      .values()
      .toList();
}
```

이를 테스트하기 위해 비슷한 테스트를 사용한다.

```java
@Test
public void whenCalled_thenReturnListOfEvenIndexedStringsVersionTwo() {
    String[] names = {"Afrim", "Bashkim", "Besim", "Lulzim", "Durim", "Shpetim"};
    List<String> expectedResult = Arrays.asList("Afrim", "Besim", "Durim");
    List<String> actualResult = StreamIndices.getEvenIndexedStrings(names);
    assertEquals(expectedResult, actualResult);
}
```

## 4. Vavre의 스트림을 사용한 반복
StreamEx 라이브러리에서 EntryStream 클래스의 `filterKeyValue()`를 사용하여 인덱스를 반복할 수도 있다. 먼저 `pom.xml`에 추가해야 한다.

```xml
<dependency>
    <groupId>one.util</groupId>
    <artifactId>streamex</artifactId>
    <version>0.8.1</version>
</dependency>
```

이전 예제를 사용하여 이 방법을 간단히 적용해 본다.

```java
public List<String> getEvenIndexedStringsVersionTwo(List<String> names) {
    return EntryStream.of(names)
      .filterKeyValue((index, name) -> index % 2 == 0)
      .values()
      .toList();
}
```

이를 테스트하기 위해 비슷한 테스트를 사용한다.

```java
@Test
public void whenCalled_thenReturnListOfEvenIndexedStringsVersionTwo() {
    String[] names = {"Afrim", "Bashkim", "Besim", "Lulzim", "Durim", "Shpetim"};
    List<String> expectedResult = Arrays.asList("Afrim", "Besim", "Durim");
    List<String> actualResult = StreamIndices.getEvenIndexedStrings(names);
    assertEquals(expectedResult, actualResult);
}
```

다음 방법으로 이 예제를 테스트할 수 있다.


```java
@Test
public void whenCalled_thenReturnListOfOddStringsVersionTwo() {
    String[] names = {"Afrim", "Bashkim", "Besim", "Lulzim", "Durim", "Shpetim"};
    List<String> expectedResult = Arrays.asList("Bashkim", "Lulzim", "Shpetim");
    List<String> actualResult = StreamIndices.getOddIndexedStringsVersionTwo(names);
    assertEquals(expectedResult, actualResult);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-indices](https://www.baeldung.com/java-stream-indices)
