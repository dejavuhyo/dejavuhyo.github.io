---
title: Java Predicate Chain
author: dejavuhyo
date: 2024-05-29 15:30:00 +0900
categories: [Language, Java]
tags: [java-predicate, predicate-chain, 자바-조건자, 조건자-체인]
---

## 1. 기본 예시
간단한 조건자를 사용하여 이름 목록을 필터링하는 방법이다.

```java
@Test
public void whenFilterList_thenSuccess(){
   List<String> names = Arrays.asList("Adam", "Alexander", "John", "Tom");
   List<String> result = names.stream()
     .filter(name -> name.startsWith("A"))
     .collect(Collectors.toList());
   
   assertEquals(2, result.size());
   assertThat(result, contains("Adam","Alexander"));
}
```

이 예에서는 Predicate를 사용하여 "A"로 시작하는 이름만 남기도록 이름 목록을 필터링했다.

```java
name -> name.startsWith("A")
```

## 2. 다중 필터
여러 Predicates를 적용하려는 경우 한 가지 옵션은 간단히 여러 필터를 연결하는 것이다.

```java
@Test
public void whenFilterListWithMultipleFilters_thenSuccess(){
    List<String> result = names.stream()
      .filter(name -> name.startsWith("A"))
      .filter(name -> name.length() < 5)
      .collect(Collectors.toList());

    assertEquals(1, result.size());
    assertThat(result, contains("Adam"));
}
```

이제 "A"로 시작하고 길이가 5보다 작은 이름을 추출하여 목록을 필터링하도록 예제를 업데이트했다.

각 Predicate에 대해 하나씩 두 개의 필터를 사용했다.

## 3. 복잡한 Predicate
이제 여러 필터를 사용하는 대신 복잡한 Predicate와 함께 하나의 필터를 사용할 수 있다.

```java
@Test
public void whenFilterListWithComplexPredicate_thenSuccess(){
    List<String> result = names.stream()
      .filter(name -> name.startsWith("A") && name.length() < 5)
      .collect(Collectors.toList());

    assertEquals(1, result.size());
    assertThat(result, contains("Adam"));
}
```

이 옵션은 비트 연산을 사용하여 원하는 만큼 복잡한 조건자를 구축할 수 있으므로 첫 번째 옵션보다 더 유연하다.

## 4. Predicates 결합
비트 연산을 사용하여 복잡한 조건자를 구축하고 싶지 않은 경우 Java 8 조건자에는 조건자를 결합하는데 사용할 수 있는 유용한 메서드가 있다.

`Predicate.and()`, `Predicate.or()` 및 `Predicate.negate()` 메서드를 사용하여 조건자를 결합한다.

### 1) Predicate.and()
예에서는 Predicate를 명시적으로 정의한 다음 `Predicate.and()`를 사용하여 결합한다.

```java
@Test
public void whenFilterListWithCombinedPredicatesUsingAnd_thenSuccess(){
    Predicate<String> predicate1 =  str -> str.startsWith("A");
    Predicate<String> predicate2 =  str -> str.length() < 5;

    List<String> result = names.stream()
      .filter(predicate1.and(predicate2))
      .collect(Collectors.toList());

    assertEquals(1, result.size());
    assertThat(result, contains("Adam"));
}
```

구문은 매우 직관적이며 메서드 이름은 작업 유형을 암시한다. `and()`를 사용하여 두 조건을 모두 충족하는 이름만 추출하여 목록을 필터링했다.

### 2) Predicate.or()
`Predicate.or()`를 사용하여 Predicate를 결합할 수도 있다.

"J"로 시작하는 이름과 길이가 4보다 작은 이름을 추출한다.

```java
@Test
public void whenFilterListWithCombinedPredicatesUsingOr_thenSuccess(){
    Predicate<String> predicate1 =  str -> str.startsWith("J");
    Predicate<String> predicate2 =  str -> str.length() < 4;

    List<String> result = names.stream()
      .filter(predicate1.or(predicate2))
      .collect(Collectors.toList());

    assertEquals(2, result.size());
    assertThat(result, contains("John","Tom"));
}
```

### 3) Predicate.negate()
Predicate를 결합할 때도 `Predicate.negate()`를 사용할 수 있다.

```java
@Test
public void whenFilterListWithCombinedPredicatesUsingOrAndNegate_thenSuccess(){
    Predicate<String> predicate1 =  str -> str.startsWith("J");
    Predicate<String> predicate2 =  str -> str.length() < 4;
    
    List<String> result = names.stream()
      .filter(predicate1.or(predicate2.negate()))
      .collect(Collectors.toList());
    
    assertEquals(3, result.size());
    assertThat(result, contains("Adam","Alexander","John"));
}
```

여기서는 `or()`와 `negate()`의 조합을 사용하여 "J"로 시작하거나 길이가 4보다 작지 않은 이름으로 목록을 필터링 했다.

### 4) Predicates 인라인 결합
`and()`, `or()` 및 `negate()`를 사용하기 위해 조건자를 명시적으로 정의할 필요가 없다.

Predicate를 캐스팅하여 인라인으로 사용할 수도 있다.

```java
@Test
public void whenFilterListWithCombinedPredicatesInline_thenSuccess(){
    List<String> result = names.stream()
      .filter(((Predicate<String>)name -> name.startsWith("A"))
      .and(name -> name.length()<5))
      .collect(Collectors.toList());

    assertEquals(1, result.size());
    assertThat(result, contains("Adam"));
}
```

## 5. Predicates 모음 결합
Predicates 컬렉션을 줄여서 연결하는 방법이다.

예에는 `Predicate.and()`를 사용하여 결합한 조건자 목록이 있다.

```java
@Test
public void whenFilterListWithCollectionOfPredicatesUsingAnd_thenSuccess(){
    List<Predicate<String>> allPredicates = new ArrayList<Predicate<String>>();
    allPredicates.add(str -> str.startsWith("A"));
    allPredicates.add(str -> str.contains("d"));
    allPredicates.add(str -> str.length() > 4);

    List<String> result = names.stream()
      .filter(allPredicates.stream().reduce(x->true, Predicate::and))
      .collect(Collectors.toList());

    assertEquals(1, result.size());
    assertThat(result, contains("Alexander"));
}
```

기본 ID를 다음과 같이 사용한다.

```java
x->true
```

그러나 `Predicate.or()`를 사용하여 이들을 결합하려는 경우에는 달라진다.

```java
@Test
public void whenFilterListWithCollectionOfPredicatesUsingOr_thenSuccess(){
    List<String> result = names.stream()
      .filter(allPredicates.stream().reduce(x->false, Predicate::or))
      .collect(Collectors.toList());
    
    assertEquals(2, result.size());
    assertThat(result, contains("Adam","Alexander"));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-predicate-chain](https://www.baeldung.com/java-predicate-chain)
