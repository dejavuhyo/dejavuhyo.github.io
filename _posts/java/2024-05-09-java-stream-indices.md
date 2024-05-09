---
title: Java 인덱스로 스트림 반복
author: dejavuhyo
date: 2024-05-09 09:08:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-indices, 스트림-인덱스]
---

## 1. 일반 자바 사용
정수 범위를 사용하여 스트림을 탐색할 수 있으며 원본 요소가 인덱스로 액세스할 수 있는 배열이나 컬렉션에 있다는 이점도 있다.

인덱스를 반복하고 이 접근 방식을 보여주는 메서드를 구현한다.

문자열 배열을 얻고 인덱스된 요소만 선택하려고 한다.

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

이제 구현을 테스트한다.

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
[proton-pack](https://mvnrepository.com/artifact/com.codepoetics/protonpack) 라이브러리에 있는 StreamUtils의 `zipWithIndex()` 메서드를 사용하여 인덱스를 반복하는 또 다른 방법을 사용할 수 있다.

먼저 `pom.xml`에 추가한다.

```xml
<dependency>
    <groupId>com.codepoetics</groupId>
    <artifactId>protonpack</artifactId>
    <version>1.16</version>
</dependency>
```

이제 코드를 살펴본다.

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

## 3. StreamEx 사용
[StreamEx](https://mvnrepository.com/artifact/one.util/streamex) 라이브러리에서 EntryStream 클래스의 `filterKeyValue()`를 사용하여 인덱스를 반복할 수도 있다. 먼저 `pom.xml`에 추가한다.

```xml
<dependency>
    <groupId>one.util</groupId>
    <artifactId>streamex</artifactId>
    <version>0.8.1</version>
</dependency>
```

이전 예제를 사용하여 이 방법을 간단히 적용한다.

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

## 4. Vavre‘s Stream 사용
또 다른 반복 방법은 Vavr(이전에는 Javaslang으로 알려짐)의 Stream 구현의 `zipWithIndex()` 메서드를 사용하는 것이다.

```xml
<dependency>
     <groupId>io.vavr</groupId>	
     <artifactId>vavr</artifactId>	
    <version>0.10.4</version>	
</dependency>
```

```java
public List<String> getOddIndexedStringsVersionTwo(String[] names) {
    return Stream
      .of(names)
      .zipWithIndex()
      .filter(tuple -> tuple._2 % 2 == 1)
      .map(tuple -> tuple._1)
      .toJavaList();
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
