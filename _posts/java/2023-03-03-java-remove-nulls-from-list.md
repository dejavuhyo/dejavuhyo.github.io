---
title: Java List 모든 Null 제거
author: dejavuhyo
date: 2023-03-03 10:15:00 +0900
categories: [Language, Java]
tags: [java-remove-null, remove-null, 리스트-널, 널-제거, 자바-리스트]
---

## 1. 일반 Java를 사용하여 List에서 Null 제거
Java 컬렉션 프레임워크는 List의 모든 null 요소를 제거하기 위한 간단한 설루션인 기본 while 루프를 제공한다.

```java
@Test
public void givenListContainsNulls_whenRemovingNullsWithPlainJava_thenCorrect() {
    List<Integer> list = Lists.newArrayList(null, 1, null);
    while (list.remove(null));

    assertThat(list, hasSize(1));
}
```

또는 다음과 같은 간단한 접근 방식을 사용할 수도 있다.

```java
@Test
public void givenListContainsNulls_whenRemovingNullsWithPlainJavaAlternative_thenCorrect() {
    List<Integer> list = Lists.newArrayList(null, 1, null);
    list.removeAll(Collections.singleton(null));

    assertThat(list, hasSize(1));
}
```

이 두 방법 모두 소스 목록을 수정합니다.

## 2. Google Guava를 사용하여 List에서 Null 제거
구아바를 사용하여 null을 제거할 수도 있고 술어를 통해 더욱 기능적인 접근 방식을 사용할 수도 있다.

```java
@Test
public void givenListContainsNulls_whenRemovingNullsWithGuavaV1_thenCorrect() {
    List<Integer> list = Lists.newArrayList(null, 1, null);
    Iterables.removeIf(list, Predicates.isNull());

    assertThat(list, hasSize(1));
}
```

또는 소스 목록을 수정하지 않으려면 Guava에서 새 필터 목록을 만들 수 있다.

```java
@Test
public void givenListContainsNulls_whenRemovingNullsWithGuavaV2_thenCorrect() {
    List<Integer> list = Lists.newArrayList(null, 1, null, 2, 3);
    List<Integer> listWithoutNulls = Lists.newArrayList(
      Iterables.filter(list, Predicates.notNull()));

    assertThat(listWithoutNulls, hasSize(3));
}
```

## 3. Apache Commons 컬렉션을 사용하여 List에서 Null 제거
이제 유사한 기능 스타일을 사용하는 Apache Commons Collections 라이브러리를 사용하는 간단한 방법이다.

```java
@Test
public void givenListContainsNulls_whenRemovingNullsWithCommonsCollections_thenCorrect() {
    List<Integer> list = Lists.newArrayList(null, 1, 2, null, 3, null);
    CollectionUtils.filter(list, PredicateUtils.notNullPredicate());

    assertThat(list, hasSize(3));
}
```

이 방법은 원래 목록도 수정한다.


## 4. Lambdas를 사용하여 List에서 Null 제거 (Java 8)
목록을 필터링하기 위해 Lambdas를 사용하는 Java 8 방법이다. 필터링 프로세스는 병렬 또는 직렬로 수행할 수 있다.

```java
@Test
public void givenListContainsNulls_whenFilteringParallel_thenCorrect() {
    List<Integer> list = Lists.newArrayList(null, 1, 2, null, 3, null);
    List<Integer> listWithoutNulls = list.parallelStream()
      .filter(Objects::nonNull)
      .collect(Collectors.toList());
}

@Test
public void givenListContainsNulls_whenFilteringSerial_thenCorrect() {
    List<Integer> list = Lists.newArrayList(null, 1, 2, null, 3, null);
    List<Integer> listWithoutNulls = list.stream()
      .filter(Objects::nonNull)
      .collect(Collectors.toList());
}

public void givenListContainsNulls_whenRemovingNullsWithRemoveIf_thenCorrect() {
    List<Integer> listWithoutNulls = Lists.newArrayList(null, 1, 2, null, 3, null);
    listWithoutNulls.removeIf(Objects::isNull);

    assertThat(listWithoutNulls, hasSize(3));
}
```

이것이 바로 목록에서 모든 null 요소를 제거하기 위한 빠르고 매우 유용한 방법이다.

## [출처 및 참고]
* [https://www.baeldung.com/java-remove-nulls-from-list](https://www.baeldung.com/java-remove-nulls-from-list)
