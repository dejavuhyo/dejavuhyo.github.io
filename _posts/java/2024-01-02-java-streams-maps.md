---
title: Java 스트림 Maps
author: dejavuhyo
date: 2024-01-02 16:10:00 +0900
categories: [Language, Java]
tags: [java-streams, streams-maps, 자바-스트림, 스트림-맵]
---

## 1. 기본 아이디어
Stream은 Collection에서 쉽게 얻을 수 있는 요소의 시퀀스라는 것이다.

맵은 순서 없이 key에서 value로 매핑되는 다른 구조를 갖는다. 그러나 이것이 맵 구조를 다른 시퀀스로 변환하여 Stream API를 사용하여 자연스러운 방식으로 작업할 수 있다는 의미는 아니다.

맵에서 다양한 Collection을 얻는 방법과 이를 Stream으로 피벗할 수 있다.

```java
Map<String, Integer> someMap = new HashMap<>();
```

key-value 쌍 세트를 얻을 수 있다.

```java
Set<Map.Entry<String, Integer>> entries = someMap.entrySet();
```

Map과 관련된 key 세트를 얻을 수도 있다.

```java
Set<String> keySet = someMap.keySet();
```

또는 값 value를 사용하여 직접 작업할 수도 있다.

```java
Collection<Integer> values = someMap.values();
```

이것들은 각각 스트림을 얻어 컬렉션을 처리하기 위한 진입점을 제공한다.

```java
Stream<Map.Entry<String, Integer>> entriesStream = entries.stream();
Stream<Integer> valuesStream = values.stream();
Stream<String> keysStream = keySet.stream();
```

## 2. Stream을 사용하여 Map의 키 ​​얻기

### 1) 입력 데이터
맵이 있다고 가정한다.

```java
Map<String, String> books = new HashMap<>();
books.put(
"978-0201633610", "Design patterns : elements of reusable object-oriented software");
books.put(
  "978-1617291999", "Java 8 in Action: Lambdas, Streams, and functional-style programming");
books.put("978-0134685991", "Effective Java");
```

우리는 "Effective Java"라는 책의 ISBN을 찾는데 관심이 있다.

### 2) 일치 검색
책 제목이 맵에 존재할 수 없으므로 관련 ISBN이 없음을 나타낼 수 있기를 원한다. Optional을 사용하여 다음과 같이 표현할 수 있다.

예에서는 해당 제목과 일치하는 책의 키에 관심이 있다고 가정해 본다.

```java
Optional<String> optionalIsbn = books.entrySet().stream()
  .filter(e -> "Effective Java".equals(e.getValue()))
  .map(Map.Entry::getKey)
  .findFirst();

assertEquals("978-0134685991", optionalIsbn.get());
```

맵에서 EntrySet을 얻는다.

제목이 "Effective Java"인 항목만 고려하려고 하므로 첫 번째 중간 작업은 필터가 된다.

전체 맵 항목이 아니라 각 항목의 키에 관심이 있다. 따라서 다음 연결 중간 작업은 바로 그 작업을 수행 한다. 이는 우리가 찾고 있는 제목과 일치하는 항목에 대한 키만 포함하는 출력으로 새 스트림을 생성하는 맵 작업이다.

하나의 결과만 원하므로 Stream의 초기 값을 Optional 개체로 제공하는 `findFirst()` 터미널 작업을 적용할 수 있다.

제목이 존재하지 않는 경우를 살펴본다.

```java
Optional<String> optionalIsbn = books.entrySet().stream()
  .filter(e -> "Non Existent Title".equals(e.getValue()))
  .map(Map.Entry::getKey).findFirst();

assertEquals(false, optionalIsbn.isPresent());
```

### 3) 여러 결과 검색
이제 문제를 변경하여 하나가 아닌 여러 결과를 반환하는 방법이다.

여러 결과를 반환하려면 맵에 다음 책을 추가한다.

```java
books.put("978-0321356680", "Effective Java: Second Edition");
```

따라서 이제 "Effective Java"로 시작하는 모든 책을 찾으면 두 개 이상의 결과를 얻게 된다.

```java
List<String> isbnCodes = books.entrySet().stream()
  .filter(e -> e.getValue().startsWith("Effective Java"))
  .map(Map.Entry::getKey)
  .collect(Collectors.toList());

assertTrue(isbnCodes.contains("978-0321356680"));
assertTrue(isbnCodes.contains("978-0134685991"));
```

이 경우 수행한 작업은 문자열 동일성을 비교하는 대신 맵의 값이 "Effective Java"로 시작하는지 확인하기 위해 필터 조건을 대체하는 것이다.

이번에는 첫 번째 결과만 선택 하는 대신 결과를 수집 하고 일치하는 항목을 List에 넣는다.

## 3. Stream을 사용하여 Map의 value 얻기
맵과 관련된 다른 문제는 제목을 기반으로 ISBN을 얻는 대신 ISBN을 기반으로 제목을 얻으려고 한다.

원본 맵을 사용한다. ISBN이 "978-0"으로 시작하는 제목을 찾고 싶다.

```java
List<String> titles = books.entrySet().stream()
  .filter(e -> e.getKey().startsWith("978-0"))
  .map(Map.Entry::getValue)
  .collect(Collectors.toList());

assertEquals(2, titles.size());
assertTrue(titles.contains(
  "Design patterns : elements of reusable object-oriented software"));
assertTrue(titles.contains("Effective Java"));
```

이 솔루션은 이전 문제 세트의 솔루션과 유사하다. 항목 세트를 스트리밍한 다음 필터링, 매핑 및 수집한다.

또한 이전과 마찬가지로 첫 번째 일치 항목만 반환하려면 List에 모든 결과를 수집하는 대신 맵 메서드 다음에 `findFirst()` 메서드를 호출하면 된다.

## [출처 및 참고]
* [https://www.baeldung.com/java-maps-streams](https://www.baeldung.com/java-maps-streams)
