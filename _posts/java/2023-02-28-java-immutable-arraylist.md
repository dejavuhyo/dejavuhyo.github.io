---
title: Java Immutable ArrayList
author: dejavuhyo
date: 2023-02-28 17:00:00 +0900
categories: [Language, Java]
tags: [java-immutable-arraylist, immutable-arraylist, java-arraylist, arraylist, 자바-불변-arraylist, 불변-arraylist, 자바-arraylist]
---

## 1. Immutable ArrayList
JDK, Guava 및 Apache Commons Collections 4를 사용하여 ArrayList를 불변으로 만드는 방법이다.

## 2. JDK 사용
JDK는 기존 컬렉션에서 수정할 수 없는 컬렉션을 가져오는 방법을 제공한다.

```java
Collections.unmodifiableList(list);
```

새 컬렉션은 이 시점에서 더 이상 수정할 수 없다.

```java
@Test(expected = UnsupportedOperationException.class)
public void givenUsingTheJdk_whenUnmodifiableListIsCreated_thenNotModifiable() {
    List<String> list = new ArrayList<>(Arrays.asList("one", "two", "three"));
    List<String> unmodifiableList = Collections.unmodifiableList(list);
    unmodifiableList.add("four");
}
```

## 3. Java 9 사용
Java 9부터 `List<E>.of(E... elements)` 정적 팩터리 메서드를 사용하여 변경할 수 없는 목록을 만들 수 있다.

```java
@Test(expected = UnsupportedOperationException.class)
public final void givenUsingTheJava9_whenUnmodifiableListIsCreated_thenNotModifiable() {
    final List<String> list = new ArrayList<>(Arrays.asList("one", "two", "three"));
    final List<String> unmodifiableList = List.of(list.toArray(new String[]{}));
    unmodifiableList.add("four");
}
```

기존 목록을 배열로 변환하는 방법에 유의해야한다. 이는 `List.of(elements)`가 vararg 매개변수를 허용하기 때문이다.

## 4. Guava 사용
Guava는 자체 버전의 `ImmutableList`를 만드는 유사한 기능을 제공한다.

```java
ImmutableList.copyOf(list);
```

마찬가지로 결과 목록은 수정할 수 없어야 한다.

```java
@Test(expected = UnsupportedOperationException.class)
public void givenUsingGuava_whenUnmodifiableListIsCreated_thenNotModifiable() {
    List<String> list = new ArrayList<>(Arrays.asList("one", "two", "three"));
    List<String> unmodifiableList = ImmutableList.copyOf(list);
    unmodifiableList.add("four");
}
```

이 작업은 실제로 보기가 아니라 원래 목록의 복사본을 만든다.

Guava는 또한 빌더를 제공한다. 단순히 List 대신 강력한 유형의 `ImmutableList`를 반환한다.

```java
@Test(expected = UnsupportedOperationException.class)
public void givenUsingGuavaBuilder_whenUnmodifiableListIsCreated_thenNoLongerModifiable() {
    List<String> list = new ArrayList<>(Arrays.asList("one", "two", "three"));
    ImmutableList<String> unmodifiableList = ImmutableList.<String>builder().addAll(list).build();
    unmodifiableList.add("four");
}
```

## 5. Apache Collections Commons 사용
Commons Collection은 수정할 수 없는 목록을 생성하는 API도 제공한다.

```java
ListUtils.unmodifiableList(list);
```

그리고 다시 결과 목록을 수정하면 `UnsupportedOperationException`이 발생한다.

```java
@Test(expected = UnsupportedOperationException.class)
public void givenUsingCommonsCollections_whenUnmodifiableListIsCreated_thenNotModifiable() {
    List<String> list = new ArrayList<>(Arrays.asList("one", "two", "three"));
    List<String> unmodifiableList = ListUtils.unmodifiableList(list);
    unmodifiableList.add("four");
}
````

## [출처 및 참고]
* [https://www.baeldung.com/java-immutable-list](https://www.baeldung.com/java-immutable-list)
