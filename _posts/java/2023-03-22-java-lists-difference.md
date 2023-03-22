---
title: Java 두 List 간의 차이점
author: dejavuhyo
date: 2023-03-22 10:55:00 +0900
categories: [Language, Java]
tags: [java-list, java-lists-difference, lists-difference, 자바-리스트, 자바-리스트-차이점, 리스트-차이점]
---

## 1. 테스트 설정
예제를 테스트하는 데 사용할 두 개의 목록을 정의한다.

```java
public class FindDifferencesBetweenListsUnitTest {
    private static final List listOne = Arrays.asList("Jack", "Tom", "Sam", "John", "James", "Jack");
    private static final List listTwo = Arrays.asList("Jack", "Daniel", "Sam", "Alan", "James", "George");
}
```

## 2. Java List API 사용
목록 하나의 복사본을 만든 다음 List 메서드 `removeAll()`을 사용하여 다른 목록과 공통된 모든 요소를 제거할 수 있다.

```java
List<String> differences = new ArrayList<>(listOne);
differences.removeAll(listTwo);
assertEquals(2, differences.size());
assertThat(differences).containsExactly("Tom", "John");
```

이것을 뒤집어서 다른 방법으로 차이점을 찾아본다.

```java
List<String> differences = new ArrayList<>(listTwo);
differences.removeAll(listOne);
assertEquals(3, differences.size());
assertThat(differences).containsExactly("Daniel", "Alan", "George");
```

또한 두 목록 사이의 공통 요소를 찾으려면 List에 retainAll 메서드도 포함되어 있다는 점에 유의해야 한다.

## 3. Streams API 사용
목록 간의 차이 필터링을 포함하여 컬렉션의 데이터에 대해 순차적 작업을 수행하는데 Java 스트림을 사용할 수 있다.

```java
List<String> differences = listOne.stream()
            .filter(element -> !listTwo.contains(element))
            .collect(Collectors.toList());
assertEquals(2, differences.size());
assertThat(differences).containsExactly("Tom", "John");
```

첫 번째 예와 같이 목록 순서를 전환하여 두 번째 목록에서 다른 요소를 찾을 수 있다.

```java
List<String> differences = listTwo.stream()
            .filter(element -> !listOne.contains(element))
            .collect(Collectors.toList());
assertEquals(3, differences.size());
assertThat(differences).containsExactly("Daniel", "Alan", "George");
```

List의 반복 호출에 주목해야 한다. `contains()`는 더 큰 목록의 경우 비용이 많이 드는 작업일 수 있다.

## 4. Third-Party 라이브러리 사용

### 1) Google Guava 사용
Guava에는 편리한 세트가 포함되어 있다. 차이 방법이지만 사용하려면 먼저 `List`를 `Set`으로 변환해야 한다.

```java
List<String> differences = new ArrayList<>(Sets.difference(Sets.newHashSet(listOne), Sets.newHashSet(listTwo)));
assertEquals(2, differences.size());
assertThat(differences).containsExactlyInAnyOrder("Tom", "John");
```

List를 Set으로 변환하면 복제 및 재정렬 효과가 있음을 유의한다.

### 2) Apache Commons Collections 사용
Apache Commons Collections의 CollectionUtils 클래스에는 `removeAll` 메서드가 포함되어 있다.

이 메서드는 List와 동일 하다. `removeAll` 결과에 대한 새 컬렉션도 생성한다.

```java
List<String> differences = new ArrayList<>((CollectionUtils.removeAll(listOne, listTwo)));
assertEquals(2, differences.size());
assertThat(differences).containsExactly("Tom", "John");
```

## 5. 중복 값 처리
두 목록에 중복 값이 포함된 경우 차이점을 찾는 방법이다.

이를 달성하려면 첫 번째 목록에서 중복 요소를 정확히 두 번째 목록에 포함된 횟수만큼 제거해야 한다.

이 예에서 값 "Jack"은 첫 번째 목록에 두 번 나타나고 두 번째 목록에는 한 번만 나타난다.

```java
List<String> differences = new ArrayList<>(listOne);
listTwo.forEach(differences::remove);
assertThat(differences).containsExactly("Tom", "John", "Jack");
```

Apache Commons Collections의 빼기 메서드를 사용하여 이를 달성할 수도 있다.

```java
List<String> differences = new ArrayList<>(CollectionUtils.subtract(listOne, listTwo));
assertEquals(3, differences.size());
assertThat(differences).containsExactly("Tom", "John", "Jack");
```

## [출처 및 참고]
* [https://www.baeldung.com/java-lists-difference](https://www.baeldung.com/java-lists-difference)
