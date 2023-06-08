---
title: Java 컬렉션 섞기
author: dejavuhyo
date: 2023-06-08 09:40:00 +0900
categories: [Language, Java]
tags: [java-shuffle-collection, shuffle-collection, 자바-컬렉션-섞기, 컬렉션-섞기]
---

## 1. List 섞기
List를 입력으로 받아 제자리에서 섞는 `java.util.Collections.shuffle` 메서드를 사용한다. 인플레이스(in-place)란 섞인 요소로 새 목록을 만드는 대신 입력으로 전달된 것과 동일한 List를 섞는 것을 의미한다.

List를 섞는 방법을 보여주는 간단한 예이다.

```java
List<String> students = Arrays.asList("Foo", "Bar", "Baz", "Qux");
Collections.shuffle(students);
```

[randomness](https://www.baeldung.com/cs/randomness)의 사용자 정의 소스를 입력으로 허용하는 `java.util.Collections.shuffle`의 두 번째 버전이 있다. 이것은 응용 프로그램에 대한 요구 사항이 있는 경우 셔플링을 결정론적 프로세스로 만드는 데 사용할 수 있다.

이 두 번째 변형을 사용하여 두 목록에서 동일한 셔플링을 달성한다.

```java
List<String> students_1 = Arrays.asList("Foo", "Bar", "Baz", "Qux");
List<String> students_2 = Arrays.asList("Foo", "Bar", "Baz", "Qux");

int seedValue = 10;

Collections.shuffle(students_1, new Random(seedValue));
Collections.shuffle(students_2, new Random(seedValue));

assertThat(students_1).isEqualTo(students_2);
```

동일한 임의성 소스(동일한 시드 값에서 초기화됨)를 사용하는 경우 생성된 난수 시퀀스는 두 셔플에서 동일하다. 따라서 섞은 후 두 목록에는 정확히 동일한 순서로 요소가 포함된다.

## 2. 정렬되지 않은 컬렉션 요소 섞기
예를 들어 `Set`, `Map` 또는 `Queue`와 같은 다른 컬렉션도 섞고 싶을 수 있지만 이러한 모든 컬렉션은 순서가 없다. 특정 순서를 유지하지 않는다.

`LinkedHashMap` 또는 Comparator가 있는 Set와 같은 일부 구현은 고정된 순서를 유지하므로 순서를 섞을 수도 없다.

그러나 먼저 List로 변환한 다음 이 List를 섞음으로써 여전히 해당 요소에 무작위로 액세스할 수 있다.

Map 요소를 섞는 간단한 예이다.

```java
Map<Integer, String> studentsById = new HashMap<>();
studentsById.put(1, "Foo");
studentsById.put(2, "Bar");
studentsById.put(3, "Baz");
studentsById.put(4, "Qux");

List<Map.Entry<Integer, String>> shuffledStudentEntries
 = new ArrayList<>(studentsById.entrySet());
Collections.shuffle(shuffledStudentEntries);

List<String> shuffledStudents = shuffledStudentEntries.stream()
  .map(Map.Entry::getValue)
  .collect(Collectors.toList());
```

마찬가지로 Set의 요소를 섞을 수 있다.

```java
Set<String> students = new HashSet<>(
  Arrays.asList("Foo", "Bar", "Baz", "Qux"));
List<String> studentList = new ArrayList<>(students);
Collections.shuffle(studentList);
```

## [출처 및 참고]
* [https://www.baeldung.com/java-shuffle-collection](https://www.baeldung.com/java-shuffle-collection)
