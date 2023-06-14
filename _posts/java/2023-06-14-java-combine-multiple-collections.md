---
title: Java 여러 컬렉션 결합
author: dejavuhyo
date: 2023-06-14 09:45:00 +0900
categories: [Language, Java]
tags: [java-collection, combine-collections, 자바-컬렉션, 컬렉션-결합]
---

## 1. 설정 컬렉션

```java
Collection<String> collectionA = Arrays.asList("S", "T");
Collection<String> collectionB = Arrays.asList("U", "V");
```

## 2. 자바 8 스트림 API 사용
Java API의 Stream 인터페이스는 컬렉션을 보다 쉽게 처리할 수 있는 유용한 메서드를 제공한다. 컬렉션을 결합하는데 사용되는 `concat()` 및 `flatMap()` 메서드이다.

Stream을 얻으면 집계 작업을 수행할 수 있다.

### 1) concat() 메서드 사용
정적 메서드 `concat()`은 요소가 첫 번째 Stream의 모든 요소와 두 번째 Stream의 모든 요소인 느리게 연결된 Stream을 생성하여 두 개의 Stream을 논리적으로 결합 한다.

아래 예제에서는 `concat()` 메서드를 사용하여 collectionA 와 collectionB를 결합한다.

```java
Stream<String> combinedStream = Stream.concat(
  collectionA.stream(),
  collectionB.stream());
```

둘 이상의 Streams를 결합해야 하는 경우 원래 호출 내에서 `concat()` 메서드를 다시 호출할 수 있다.

```java
Stream<String> combinedStream = Stream.concat(
  Stream.concat(collectionA.stream(), collectionB.stream()), 
  collectionC.stream());
```

Java 8 스트림은 재사용할 수 없으므로 변수에 할당할 때 이를 고려해야 한다.

### 2) flatMap() 메서드 사용 
`flatMap()` 메서드는 이 Stream의 각 요소를 제공된 매핑 함수를 각 요소에 적용하여 생성된 매핑된 Stream의 내용으로 교체한 후 Stream을 반환한다.

아래 예제는 `flatMap()` 메서드를 사용하여 컬렉션을 병합하는 방법이다. 처음에는 요소가 두 컬렉션인 Stream을 가져온 다음 병합된 목록으로 수집하기 전에 Stream을 평면화한다.

```java
Stream<String> combinedStream = Stream.of(collectionA, collectionB)
  .flatMap(Collection::stream);
Collection<String> collectionCombined = 
  combinedStream.collect(Collectors.toList());
```

## 3. 구아바 사용
Google의 Guava 라이브러리는 컬렉션에서 작동하기 위한 몇 가지 편리한 방법을 제공하며 Java 6 이상에서 사용할 수 있다.

### 1) Iterables.concat() 메서드 사용
`Iterables.concat()` 메서드는 컬렉션 병합에 사용되는 Guava의 편리한 메서드 중 하나이다.

```java
Iterable<String> combinedIterables = Iterables.unmodifiableIterable(
  Iterables.concat(collectionA, collectionA));
```

반환된 Iterable은 컬렉션 으로 변환할 수 있다.

```java
Collection<String> collectionCombined = Lists.newArrayList(combinedIterables);
```

### 2) 메이븐 종속성
프로젝트에 Guava 라이브러리를 포함하려면 Maven `pom.xml` 파일에 다음 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.0.1-jre</version>
</dependency>
```

[Maven Central](https://central.sonatype.com/artifact/com.google.guava/guava/32.0.1-jre) 리포지토리에서 최신 버전의 Guava 라이브러리를 찾을 수 있다.

## 4. Apache Commons Collection 사용
Apache Commons Collections는 다양한 컬렉션 작업을 지원하는 또 다른 유틸리티 라이브러리이다. 라이브러리는 컬렉션을 결합하는데 사용할 수 있는 두 가지 유틸리티 메서드를 제공한다.

### 1) IterableUtils.chainedIterable() 메서드 사용
IterableUtils 클래스는 Iterable 인스턴스에 대한 유틸리티 메서드 및 데코레이터를 제공 한다. 여러 Iterable을 하나로 결합하는데 사용할 수 있는 `chainedIterable()` 메서드를 제공한다.

```java
Iterable<String> combinedIterables = IterableUtils.chainedIterable(collectionA, collectionB);
```

### 2) CollectionUtils.union() 메서드 사용
Collection 인스턴스에 대한 유틸리티 메서드 및 데코레이터는 CollectionUtils 클래스에서 제공한다. 이 클래스의 `union()` 메서드는 주어진 Iterable 인스턴스의 합집합을 포함하는 Collection을 반환한다.

```java
Iterable<String> combinedIterables = CollectionUtils.union(collectionA, collectionB);
```

`union()` 메서드의 경우 반환된 컬렉션의 각 요소의 카디널리티는 주어진 두 Iterables에서 해당 요소의 카디널리티의 최대값과 같다. 즉, 결합된 컬렉션은 첫 번째 컬렉션의 요소와 첫 번째 컬렉션에 없는 두 번째 컬렉션의 요소로만 구성된다.

### 3) 메이븐 종속성
프로젝트에 Apache Commons Collections 라이브러리를 포함하려면 Maven `pom.xml` 파일에 다음 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.1</version>
</dependency>
```

[Maven Central](https://central.sonatype.com/artifact/org.apache.commons/commons-collections4/4.4) 리포지토리에서 최신 버전의 Apache Commons 라이브러리를 찾을 수 있다.

## 5. 자바 7 사용
여전히 Java 7을 사용 중이고 Guava와 같은 타사 라이브러리를 사용하지 않으려면 `addAll()` 메서드를 사용하여 여러 컬렉션의 요소를 결합하거나 자체 유틸리티 메서드를 작성하여 Iterables를 결합할 수 있다.

### 1) addAll() 메서드 사용
 컬렉션을 결합하는 가장 간단한 솔루션은 다음 List 예제와 같이 `addAll()` 메서드를 사용 하는 것이지만 이 메서드는 처음 두 컬렉션에 있는 동일한 개체에 대한 추가 참조를 사용하여 새 컬렉션을 생성한다는 점에 유의할 가치가 있다.

```java
List<String> listC = new ArrayList<>();
listC.addAll(listA);
listC.addAll(listB);
```

### 2) 사용자 지정 concat() 메서드 작성
아래 예제는 두 개의 Iterable을 허용 하고 병합된 Iterable 객체를 반환하는 `concat()` 메서드를 정의한다.

```java
public static <E> Iterable<E> concat(
  Iterable<? extends E> i1,
  Iterable<? extends E> i2) {
        return new Iterable<E>() {
            public Iterator<E> iterator() {
                return new Iterator<E>() {
                    Iterator<? extends E> listIterator = i1.iterator();
                    Boolean checkedHasNext;
                    E nextValue;
                    private boolean startTheSecond;

                    void theNext() {
                        if (listIterator.hasNext()) {
                            checkedHasNext = true;
                            nextValue = listIterator.next();
                        } else if (startTheSecond)
                            checkedHasNext = false;
                        else {
                            startTheSecond = true;
                            listIterator = i2.iterator();
                            theNext();
                        }
                    }

                    public boolean hasNext() {
                        if (checkedHasNext == null)
                            theNext();
                        return checkedHasNext;
                    }

                    public E next() {
                        if (!hasNext())
                            throw new NoSuchElementException();
                        checkedHasNext = null;
                        return nextValue;
                    }

                    public void remove() {
                        listIterator.remove();
                    }
                };
            }
        };
    }
```

`concat()` 메서드는 두 컬렉션을 인수로 전달하여 호출할 수 있다.

```java
Iterable<String> combinedIterables = concat(collectionA, collectionB);
Collection<String> collectionCombined = makeListFromIterable(combinedIterables);
```

Iterable을 List로 사용할 수 있어야 하는 경우 Iterable의 멤버를 사용하여 List를 생성하는 `makeListFromIterable()` 메서드를 사용할 수 있다.

```java
public static <E> List<E> makeListFromIterable(Iterable<E> iter) {
    List<E> list = new ArrayList<E>();
    for (E item : iter) {
        list.add(item);
    }
    return list;
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-combine-multiple-collections](https://www.baeldung.com/java-combine-multiple-collections)
