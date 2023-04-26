---
title: Java Map.Entry 사용
author: dejavuhyo
date: 2023-04-26 10:45:00 +0900
categories: [Language, Java]
tags: [java-map-entry, map-entry, 자바-맵-엔트리, 맵-엔트리]
---

## 1. 맵 반복 최적화
저자의 이름을 키로 하는 책 제목 맵이 있다고 가정한다.

```java
Map<String, String> map = new HashMap<>();

map.put("Robert C. Martin", "Clean Code");
map.put("Joshua Bloch", "Effective Java");
```

맵에서 모든 키와 값을 가져오는 두 가지 방법을 비교해 본다.

### 1) Map.keySet 사용

```java
for (String key : bookMap.keySet()) {
    System.out.println("key: " + key + " value: " + bookMap.get(key));
}
```

여기서 루프는 keySet을 반복한다. 각 키에 대해 `Map.get`을 사용하여 해당 값을 얻는다. 이것은 맵의 모든 항목을 사용하는 확실한 방법이지만 각 항목에 대해 두 가지 작업이 필요하다. 하나는 다음 키를 가져오고 다른 하나는 get으로 값을 조회하는 것이다.

맵에서 키만 필요한 경우 keySet이 좋은 옵션이다. 그러나 키와 값을 모두 가져오는 더 빠른 방법이 있다.

### 2) Map.entrySet 사용
entrySet을 사용하도록 반복을 다시 작성한다.

```java
for (Map.Entry<String, String> book: bookMap.entrySet()) {
    System.out.println("key: " + book.getKey() + " value: " + book.getValue());
}
```

이 예제에서 루프는 `Map.Entry` 개체의 컬렉션에 있다. `Map.Entry`는 키와 값을 하나의 클래스에 함께 저장하므로 둘 다 단일 작업으로 가져 온다.

동일한 규칙이 Java 8 스트림 작업 사용에 적용된다. entrySet을 통해 스트리밍 하고 Entry 객체로 작업하는 것이 더 효율적이며 필요한 코드가 더 적을 수 있다.

## 2. 튜플 작업
튜플은 요소의 수와 순서가 고정된 데이터 구조이다. `Map.Entry`는 키와 값이라는 두 가지 요소를 저장하는 튜플이라고 생각할 수 있다. 그러나 `Map.Entry`는 인터페이스이므로 구현 클래스가 필요하다. JDK에서 제공하는 한 가지 구현인 `AbstractMap.SimpleEntry`를 살펴본다.

### 1) 튜플 생성
먼저 Book 클래스를 생성한다.

```java
public class Book {
    private String title;
    private String author;

    public Book(String title, String author) {
        this.title = title;
        this.author = author;
    }
    ...
```

다음으로 ISBN을 키로, Book 개체를 값으로 사용하여 `Map.Entry` 튜플을 생성한다.

```java
Map.Entry<String, Book> tuple;
```

마지막으로 `AbstractMap.SimpleEntry`를 사용하여 튜플을 인스턴스화한다.

```java
tuple = new AbstractMap.SimpleEntry<>("9780134685991", new Book("Effective Java 3d Edition", "Joshua Bloch"));
```

### 2) 순서가 있는 튜플 목록 생성
튜플로 작업할 때 튜플을 정렬된 목록으로 사용하는 것이 종종 유용하다.

먼저 튜플 목록을 정의한다.

```java
List<Map.Entry<String, Book>> orderedTuples = new ArrayList<>();
```

둘째, 목록에 몇 가지 항목을 추가한다.

```java
orderedTuples.add(new AbstractMap.SimpleEntry<>("9780134685991", 
  new Book("Effective Java 3d Edition", "Joshua Bloch")));
orderedTuples.add(new AbstractMap.SimpleEntry<>("9780132350884", 
  new Book("Clean Code","Robert C Martin")));
```

### 3) 맵과 비교
Map과 차이점을 비교하기 위해 이미 존재하는 키로 새 항목을 추가한다.

```java
orderedTuples.add(new AbstractMap.SimpleEntry<>("9780132350884", 
  new Book("Clean Code", "Robert C Martin")));
```

둘째, 목록을 반복하여 모든 키와 값을 표시한다.

```java
for (Map.Entry<String, Book> tuple : orderedTuples) {
    System.out.println("key: " + tuple.getKey() + " value: " + tuple.getValue());
}
```

마지막으로 출력을 확인한다.

```text
key: 9780134685991 value: Book{title='Effective Java 3d Edition', author='Joshua Bloch'}
key: 9780132350884 value: Book{title='Clean Code', author='Robert C Martin'}
key: 9780132350884 value: Book{title='Clean Code', author='Robert C Martin'}
```

각 키가 고유해야 하는 기본 Map과 달리 중복 키를 가질 수 있다. 이는 SimpleEntry 개체를 저장하기 위해 List 구현을 사용했기 때문이다. 즉, 모든 개체가 서로 독립적이다.

### 4) 엔트리 개체 목록
Entry의 목적은 일반 튜플로 작동하지 않는다는 점에 유의해야 한다. 라이브러리 클래스는 종종 이러한 목적을 위해 일반 Pair 클래스를 제공한다.

그러나 맵에 대한 데이터를 준비하거나 맵에서 데이터를 추출하는 동안 일시적으로 항목 목록으로 작업해야 할 수도 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-map-entry](https://www.baeldung.com/java-map-entry)
