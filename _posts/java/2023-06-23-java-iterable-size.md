---
title: Java Iterator 크기
author: dejavuhyo
date: 2023-06-23 09:45:00 +0900
categories: [Language, Java]
tags: [java-iterator-size, iterator-size, 자바-반복자-크기, 반복자-크기]
---

## 1. Iterable 및 Iterator
Iterable은 Java 컬렉션 클래스의 주요 인터페이스 중 하나이다.

Collection 인터페이스는 Iterable을 확장하므로 Collection의 모든 자식 클래스도 Iterable을 구현 한다.

Iterable에는 Iterator를 생성하는 단 하나의 메서드가 있다.

```java
public interface Iterable<T> {
    public Iterator<T> iterator();
}
```

이 Iterator를 사용하여 Iterable의 요소를 반복할 수 있다.

## 2. Core Java를 사용한 반복 가능한 크기

### 1) for-each Loop
Iterable을 구현하는 모든 클래스는 Java의 for-each 루프에 적합하다.

이를 통해 크기를 얻기 위해 카운터를 증가시키면서 Iterable의 요소를 반복할 수 있다.

```java
int counter = 0;
for (Object i : data) {
    counter++;
}
return counter;
```

### 2) Collection.size()
대부분의 경우 Iterable은 List 또는 Set과 같은 Collection의 인스턴스이다.

이 경우 Iterable의 유형을 확인 하고 `size()` 메서드를 호출하여 요소 수를 얻을 수 있다.

```java
if (data instanceof Collection) {
    return ((Collection<?>) data).size();
}
```

`size()`에 대한 호출은 일반적으로 전체 컬렉션을 반복하는 것보다 훨씬 빠르다.

다음은 위의 두 솔루션 조합을 보여주는 예이다.

```java
public static int size(Iterable data) {

    if (data instanceof Collection) {
        return ((Collection<?>) data).size();
    }
    int counter = 0;
    for (Object i : data) {
        counter++;
    }
    return counter;
}
```

### 3) Stream.count()
Java 8을 사용하는 경우 Iterable에서 스트림을 만들 수 있다.

그런 다음 스트림 개체를 사용하여 Iterable의 요소 수를 가져올 수 있다.

```java
return StreamSupport.stream(data.spliterator(), false).count();
```

## 3. 타사 라이브러리를 사용한 반복 가능한 크기

### 1) IterableUtils.size()
Apache Commons Collections 라이브러리에는 Iterable 인스턴스에 대한 정적 유틸리티 메서드를 제공하는 [IterableUtils](https://commons.apache.org/proper/commons-collections/apidocs/org/apache/commons/collections4/IterableUtils.html) 클래스가 있다.

Maven Central에서 최신 종속 항목을 가져와야 한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.1</version>
</dependency>
```

크기를 얻기 위해 Iterable 객체에서 IterableUtils의 `size()` 메서드를 호출할 수 있다.

```java
return IterableUtils.size(data);
```

### 2) Iterables.size()
마찬가지로 Google Guava 라이브러리는 Iterable 인스턴스에서 작동하도록 [Iterables](https://guava.dev/releases/snapshot/api/docs/com/google/common/collect/Iterables.html#size(java.lang.Iterable)) 클래스에 정적 유틸리티 메서드 모음을 제공한다.

Maven Central에서 최신 종속 항목을 가져와야 한다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.0.1-jre</version>
</dependency>
```

Iterables 클래스에서 정적 `size()` 메서드를 호출하면 요소 수가 제공된다.

```java
return Iterables.size(data);
```

## [출처 및 참고]
* [https://www.baeldung.com/java-iterable-size#2-iterablessize](https://www.baeldung.com/java-iterable-size#2-iterablessize)
