---
title: Guava Collections
author: dejavuhyo
date: 2023-07-07 08:25:00 +0900
categories: [Language, Java]
tags: [guava-collections, guava, collections, 구아바-컬렉션, 구아바, 컬렉션]
---

## 1. List<Parent>를 List<Child>로 다운캐스트
Java의 non-covariant 생성 컬렉션에 대한 해결 방법이다.

```java
class CastFunction<F, T extends F> implements Function<F, T> {
    @Override
    public final T apply(final F from) {
        return (T) from;
    }
}
List<TypeParent> originalList = Lists.newArrayList();
List<TypeChild> theList = Lists.transform(originalList, new CastFunction<TypeParent, TypeChild>());
```

구아바가 없는 더 간단한 대안으로 2개의 캐스트 작업을 포함한다.

```java
List<Number> originalList = Lists.newArrayList();
List<Integer> theList = (List<Integer>) (List<? extends Number>) originalList;
```

## 2. 컬렉션에 iterable 추가

```java
Iterable<String> iter = Lists.newArrayList();
Collection<String> collector = Lists.newArrayList();
Iterables.addAll(collector, iter);
```

## 3. 맞춤 일치 규칙에 따라 컬렉션에 요소가 포함되어 있는지 확인

```java
Iterable<String> theCollection = Lists.newArrayList("a", "bc", "def");
    boolean contains = Iterables.any(theCollection, new Predicate<String>() {
    @Override
    public boolean apply(final String input) {
        return input.length() == 1;
    }
});
assertTrue(contains);
```

### 1) 검색을 이용한 대체 솔루션

```java
Iterable<String> theCollection = Sets.newHashSet("a", "bc", "def");
boolean contains = Iterables.find(theCollection, new Predicate<String>() {
    @Override
    public boolean apply(final String input) {
       return input.length() == 1;
    }
}) != null;
assertTrue(contains);
```

### 2) Sets에만 적용되는 대체 솔루션

```java
Set<String> theCollection = Sets.newHashSet("a", "bc", "def");
boolean contains = !Sets.filter(theCollection, new Predicate<String>() {
    @Override
    public boolean apply(final String input) {
        return input.length() == 1;
    }
}).isEmpty();
assertTrue(contains);
```

## 4. 아무 것도 찾을 수 없을 때 Iterables.find 의 NoSuchElementException

```java
Iterable<String> theCollection = Sets.newHashSet("abcd", "efgh", "ijkl");
Predicate<String> inputOfLengthOne = new Predicate<String>() {
    @Override
    public boolean apply(final String input) {
        return input.length() == 1;
    }
};
String found = Iterables.find(theCollection, inputOfLengthOne);
```

NoSuchElementException 예외가 발생한다.

```text
java.util.NoSuchElementException
	at com.google.common.collect.AbstractIterator.next(AbstractIterator.java:154)
	at com.google.common.collect.Iterators.find(Iterators.java:712)
	at com.google.common.collect.Iterables.find(Iterables.java:643)
```

해결 방법: 기본 반환 값을 인수로 사용하고 원하는 동작에 대해 null로 호출할 수 있는 오버로드된 find 메서드가 있다.

```java
String found = Iterables.find(theCollection, inputOfLengthOne, null);
```

## 5. 컬렉션에서 모든 null 값 제거

```java
List<String> values = Lists.newArrayList("a", null, "b", "c");
Iterable<String> withoutNulls = Iterables.filter(values, Predicates.notNull());
```

## 6. 변경할 수 없는 List/Set/Map을 직접 생성

```java
ImmutableList<String> immutableList = ImmutableList.of("a", "b", "c");
ImmutableSet<String> immutableSet = ImmutableSet.of("a", "b", "c");
ImmutableMap<String, String> imuttableMap = ImmutableMap.of("k1", "v1", "k2", "v2", "k3", "v3");
```

## 7. 표준 컬렉션에서 변경할 수 없는 List/Set/Map 생성

```java
List<String> muttableList = Lists.newArrayList();
ImmutableList<String> immutableList = ImmutableList.copyOf(muttableList);

Set<String> muttableSet = Sets.newHashSet();
ImmutableSet<String> immutableSet = ImmutableSet.copyOf(muttableSet);

Map<String, String> muttableMap = Maps.newHashMap();
ImmutableMap<String, String> imuttableMap = ImmutableMap.copyOf(muttableMap);
```

빌더를 사용한 대체 솔루션이다.

```java
List<String> muttableList = Lists.newArrayList();
ImmutableList<String> immutableList = ImmutableList.<String> builder().addAll(muttableList).build();

Set<String> muttableSet = Sets.newHashSet();
ImmutableSet<String> immutableSet = ImmutableSet.<String> builder().addAll(muttableSet).build();

Map<String, String> muttableMap = Maps.newHashMap();
ImmutableMap<String, String> imuttableMap = ImmutableMap.<String, String> builder().putAll(muttableMap).build();
```

## [출처 및 참고]
* [https://www.baeldung.com/guava-collections](https://www.baeldung.com/guava-collections)
