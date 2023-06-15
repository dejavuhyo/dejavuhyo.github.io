---
title: Java List 또는 Collection 최대/최소 찾기
author: dejavuhyo
date: 2023-06-15 09:25:00 +0900
categories: [Language, Java]
tags: [java-collection, collection-min, collection-max, list-min, list-max, 자바-컬렉션, 컬렉션-최대, 컬렉션-최소]
---

## 1. 정수 목록에서 최대값 찾기
메서드 참조를 허용하는 `java.util.Stream` 인터페이스 를 통해 제공되는 `max()` 메서드를 사용할 수 있다.

```java
@Test
public void whenListIsOfIntegerThenMaxCanBeDoneUsingIntegerComparator() {
    // given
    List<Integer> listOfIntegers = Arrays.asList(1, 2, 3, 4, 56, 7, 89, 10);
    Integer expectedResult = 89;

    // then
    Integer max = listOfIntegers
      .stream()
      .mapToInt(v -> v)
      .max().orElseThrow(NoSuchElementException::new);

    assertEquals("Should be 89", expectedResult, max);
}
```

코드를 살펴본다.

* List에서 `stream()` 메서드를 호출하여 목록에서 값 스트림 가져온다.

* 정수 스트림을 얻기 위해 스트림에서 `mapToInt(value -> value)` 호출한다.

* 최대 값을 얻기 위해 스트림에서 `max()` 메서드 호출한다.

* `orElseThrow()`를 호출하여 `max()`에서 값을 받지 못한 경우 예외 발생한다.

## 2. 사용자 지정 개체로 최소값 찾기
사용자 지정 개체에서 최소/최대를 찾기 위해 선호하는 정렬 논리에 대한 람다 식을 제공할 수 있다.

먼저 사용자 지정 POJO를 정의한다.

```java
class Person {
    String name;
    Integer age;
      
    // standard constructors, getters and setters
}
```

최소 연령의 Person 객체를 찾고자 한다.

```java
@Test
public void whenListIsOfPersonObjectThenMinCanBeDoneUsingCustomComparatorThroughLambda() {
    // given
    Person alex = new Person("Alex", 23);
    Person john = new Person("John", 40);
    Person peter = new Person("Peter", 32);
    List<Person> people = Arrays.asList(alex, john, peter);

    // then
    Person minByAge = people
      .stream()
      .min(Comparator.comparing(Person::getAge))
      .orElseThrow(NoSuchElementException::new);

    assertEquals("Should be Alex", alex, minByAge);
}
```

코드를 살펴본다.

* 목록에서 `stream()` 메서드를 호출하여 목록에서 값 스트림 가져온다.

* 스트림에서 `min()` 메서드를 호출하여 최소값을 가져온다. 람다 함수를 비교자로 전달하고 이것은 최소값을 결정하기 위한 정렬 논리를 결정하는 데 사용된다.

* `min()`에서 값을 받지 못한 경우 `orElseThrow()`를 호출하여 예외 발생한다.

## 3. 인덱스 번호와 함께 ArrayList에서 최소/최대 찾기
ArrayList의 최소값 또는 최대값을 결정하기 위해 앞에서 본 메서드 또는 Java Collections 클래스의 `min()` 및 `max()` 메서드를 사용할 수 있다. 이러한 메서드는 각각 지정된 컬렉션의 최소 및 최대 요소를 반환한다.

또한 ArrayList 클래스의 `indexOf()` 메서드를 사용하여 목록의 요소 인덱스를 가져올 수 있다. 이 메서드는 목록에서 요소가 처음 나타나는 인덱스를 반환한다.

예제 이다.

```java
List<Integer> listOfIntegers = Arrays.asList(11, 13, 9, 20, 7, 3, 30);
Integer expectedMinValue = 3;
Integer expectedMinIndex = 5;

Integer minValue = Collections.min(listOfIntegers);
Integer minIndex = listOfIntegers.indexOf(minValue);

assertEquals(minValue, expectedMinValue);
assertEquals(minIndex, expectedMinIndex);
```

먼저 값을 저장할 Integer 목록을 정의한다. 그런 다음 `Collections.min()`을 사용하여 목록의 최소값을 가져온다. 이 경우 이 값은 3이다. 마지막으로 `listOfIntegers.indexOf()`를 사용하여 목록에서 이 값의 인덱스를 가져온다. 여기서는 5이다.

## [출처 및 참고]
* [https://www.baeldung.com/java-collection-min-max](https://www.baeldung.com/java-collection-min-max)
