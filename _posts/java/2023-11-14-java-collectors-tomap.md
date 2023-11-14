---
title: Java Collectors toMap
author: dejavuhyo
date: 2023-11-14 23:40:00 +0900
categories: [Language, Java]
tags: [java-collectors, collectors-tomap]
---

## 1. List to Map
List를 Map으로 변환하는 가장 간단한 방법이다.

Book 클래스를 정의하는 방법은 다음과 같다.

```java
class Book {
    private String name;
    private int releaseYear;
    private String isbn;
    
    // getters and setters
}
```

그리고 코드를 검증하기 위해 책 목록을 만든다.

```java
List<Book> bookList = new ArrayList<>();
bookList.add(new Book("The Fellowship of the Ring", 1954, "0395489318"));
bookList.add(new Book("The Two Towers", 1954, "0345339711"));
bookList.add(new Book("The Return of the King", 1955, "0618129111"));
```

이 시나리오에서는 `toMap()` 메서드의 다음 오버로드를 사용한다.

```java
Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
  Function<? super T, ? extends U> valueMapper)
```

toMap을 사용하면 map의 키와 값을 얻는 방법에 대한 전략을 나타낼 수 있다.

```java
public Map<String, String> listToMap(List<Book> books) {
    return books.stream().collect(Collectors.toMap(Book::getIsbn, Book::getName));
}
```

그리고 그것이 작동하는지 쉽게 검증할 수 있다.

```java
@Test
public void whenConvertFromListToMap() {
    assertTrue(convertToMap.listToMap(bookList).size() == 3);
}
```

## 2. 주요 갈등 해결
위의 예는 잘 작동했지만 중복 키가 있으면 어떻게 되는지 확인한다.

각 책의 출시 연도 별로 지도에 키를 입력했다고 가정해 본다.

```java
public Map<Integer, Book> listToMapWithDupKeyError(List<Book> books) {
    return books.stream().collect(
      Collectors.toMap(Book::getReleaseYear, Function.identity()));
}
```

이전 도서 목록을 보면 IllegalStateException이 표시된다.

```java
@Test(expected = IllegalStateException.class)
public void whenMapHasDuplicateKey_without_merge_function_then_runtime_exception() {
    convertToMap.listToMapWithDupKeyError(bookList);
}
```

이 문제를 해결하려면 추가 매개변수인 mergeFunction과 함께 다른 메서드를 사용해야 한다.

```java
Collector<T, ?, M> toMap(Function<? super T, ? extends K> keyMapper,
  Function<? super T, ? extends U> valueMapper,
  BinaryOperator<U> mergeFunction)
```

충돌이 발생하는 경우 기존 항목을 유지함을 나타내는 병합 기능이다.

```java
public Map<Integer, Book> listToMapWithDupKey(List<Book> books) {
    return books.stream().collect(Collectors.toMap(Book::getReleaseYear, Function.identity(),
      (existing, replacement) -> existing));
}
```

즉, 첫 번째 승리 동작을 얻는다.

```java
@Test
public void whenMapHasDuplicateKeyThenMergeFunctionHandlesCollision() {
    Map<Integer, Book> booksByYear = convertToMap.listToMapWithDupKey(bookList);
    assertEquals(2, booksByYear.size());
    assertEquals("0395489318", booksByYear.get(1954).getIsbn());
}
```

## 3. 기타 Map 유형
기본적으로 `toMap()` 메서드는 HashMap을 반환한다.

하지만 다른 Map 구현을 반환할 수 있다.

```java
Collector<T, ?, M> toMap(Function<? super T, ? extends K> keyMapper,
  Function<? super T, ? extends U> valueMapper,
  BinaryOperator<U> mergeFunction,
  Supplier<M> mapSupplier)
```

여기서 mapSupplier는 결과가 포함된 새로운 빈 맵을 반환하는 함수이다.

### 1) List to ConcurrentMap
동일한 예를 들어 ConcurrentHashMap을 반환하는 mapSupplier 함수를 추가해 본다.

```java
public Map<Integer, Book> listToConcurrentMap(List<Book> books) {
    return books.stream().collect(Collectors.toMap(Book::getReleaseYear, Function.identity(),
      (o1, o2) -> o1, ConcurrentHashMap::new));
}
```

계속해서 코드를 테스트한다.

```java
@Test
public void whenCreateConcurrentHashMap() {
    assertTrue(convertToMap.listToConcurrentMap(bookList) instanceof ConcurrentHashMap);
}
```

### 2) Sorted Map
마지막으로 정렬된 지도를 반환하는 방법이다. 이를 위해 TreeMap을 mapSupplier 매개변수로 사용한다.

TreeMap은 기본적으로 키의 자연스러운 순서에 따라 정렬되므로 명시적으로 책을 직접 정렬할 필요가 없다.

```java
public TreeMap<String, Book> listToSortedMap(List<Book> books) {
    return books.stream() 
      .collect(
        Collectors.toMap(Book::getName, Function.identity(), (o1, o2) -> o1, TreeMap::new));
}
```

따라서 반환된 TreeMap은 책 이름을 기준으로 알파벳 순서로 정렬된다.

```java
@Test
public void whenMapisSorted() {
    assertTrue(convertToMap.listToSortedMap(bookList).firstKey().equals(
      "The Fellowship of the Ring"));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-collectors-tomap](https://www.baeldung.com/java-collectors-tomap)
