---
title: Java 9 새로운 Stream Collectors
author: dejavuhyo
date: 2023-11-10 19:40:00 +0900
categories: [Language, Java]
tags: [java-stream-collectors, stream, 자바-스트림, 자바-수집기]
---

## 1. Filtering Collector
`Collectors.filtering`은 `Stream filter()`와 유사하다. 입력 요소를 필터링하는데 사용되지만 다른 시나리오에 사용된다. Stream의 필터는 스트림 체인에서 사용되는 반면 필터링은 groupingBy와 함께 사용되도록 설계된 Collector 이다.

Stream의 필터를 사용하면 값을 먼저 필터링한 다음 그룹화한다. 이런 방식으로 필터링된 값은 사라지고 흔적도 남지 않는다. 추적이 필요한 경우 먼저 그룹화한 다음 실제로 `Collectors.filtering`이 수행하는 필터링을 적용해야 한다.

`Collectors.filtering`은 입력 요소를 필터링하는 함수와 필터링된 요소를 수집하는 수집기를 사용한다.

```java
@Test
public void givenList_whenSatifyPredicate_thenMapValueWithOccurences() {
    List<Integer> numbers = List.of(1, 2, 3, 5, 5);

    Map<Integer, Long> result = numbers.stream()
      .filter(val -> val > 3)
      .collect(Collectors.groupingBy(i -> i, Collectors.counting()));

    assertEquals(1, result.size());

    result = numbers.stream()
      .collect(Collectors.groupingBy(i -> i,
        Collectors.filtering(val -> val > 3, Collectors.counting())));

    assertEquals(4, result.size());
}
```

## 2. FlatMapping Collector
`Collectors.FlatMapping`은 `Collectors.mapping`과 유사 하지만 더 세분화된 목표를 가지고 있다. 두 수집기 모두 요소가 수집되는 함수와 수집기를 사용하지만 flatMapping 함수는 수집기에 의해 누적되는 요소 스트림을 허용한다.

다음 모델 클래스를 살펴본다.

```java
class Blog {
    private String authorName;
    private List<String> comments;
      
    // constructor and getters
}
```

`Collectors.FlatMapping`을 사용 하면 중간 컬렉션을 건너뛰고 `Collectors.groupingBy`에 의해 정의된 그룹에 매핑되는 단일 컨테이너에 직접 쓸 수 있다.

```java
@Test
public void givenListOfBlogs_whenAuthorName_thenMapAuthorWithComments() {
    Blog blog1 = new Blog("1", "Nice", "Very Nice");
    Blog blog2 = new Blog("2", "Disappointing", "Ok", "Could be better");
    List<Blog> blogs = List.of(blog1, blog2);

    Map<String,  List<List<String>>> authorComments1 = blogs.stream()
     .collect(Collectors.groupingBy(Blog::getAuthorName, 
       Collectors.mapping(Blog::getComments, Collectors.toList())));

    assertEquals(2, authorComments1.size());
    assertEquals(2, authorComments1.get("1").get(0).size());
    assertEquals(3, authorComments1.get("2").get(0).size());

    Map<String, List<String>> authorComments2 = blogs.stream()
      .collect(Collectors.groupingBy(Blog::getAuthorName, 
        Collectors.flatMapping(blog -> blog.getComments().stream(), 
        Collectors.toList())));

    assertEquals(2, authorComments2.size());
    assertEquals(2, authorComments2.get("1").size());
    assertEquals(3, authorComments2.get("2").size());
}
```

`Collectors.mapping`은 그룹화 된 모든 작성자의 주석을 수집기의 컨테이너(예: List)에 매핑하는 반면 이 중간 컬렉션은 수집기의 컨테이너에 매핑될 주석 목록의 직접적인 스트림을 제공하므로 flatMapping을 사용하여 제거된다.

## [출처 및 참고]
* [https://www.baeldung.com/java9-stream-collectors](https://www.baeldung.com/java9-stream-collectors)
