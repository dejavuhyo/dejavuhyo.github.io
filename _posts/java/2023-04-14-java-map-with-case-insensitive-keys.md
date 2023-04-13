---
title: Java 대소문자를 구분하지 않는 키가 있는 맵
author: dejavuhyo
date: 2023-04-14 08:15:00 +0900
tags: [java-map-case-insensitive-keys, java-map, map-case-insensitive-keys, caseinsensitivemap, linkedcaseinsensitivemap, 자바-맵]
categories: [Language, Java]
---

## 1. 대소문자를 구분하지 않는 키가 있는 맵
하나의 항목이 있는 Map<String, Integer>가 있다.

![first-entry](/assets/img/2023-04-14-java-map-with-case-insensitive-keys/first-entry.png)

```java
map.put("ABC", 2);
```

대소문자를 구분하는 키가 있는 맵으로 작업할 때 다음 두 항목으로 끝난다.

![sensetive-key](/assets/img/2023-04-14-java-map-with-case-insensitive-keys/sensetive-key.png)

그러나 대소문자를 구분하지 않는 키가 있는 맵으로 작업할 때 내용은 다음과 같다.

![insensetive-key](/assets/img/2023-04-14-java-map-with-case-insensitive-keys/insensetive-key.png)

다음 예제에서는 인기 있는 일부 Map 구현(TreeMap, HashMap 및 LinkedHashMap)의 대소문자를 구분하지 않는 구현이다.

## 2. TreeMap
TreeMap은 NavigableMap의 구현이다. 즉, 삽입 후 항상 지정된 Comparator를 기반으로 항목을 정렬한다. 또한 TreeMap은 Comparator를 사용하여 삽입된 키가 중복인지 새 키인지 확인한다.

따라서 대소문자를 구분하지 않는 String Comparator를 제공하면 대소문자를 구분하지 않는 TreeMap을 얻게 된다.

운 좋게도 String은 이미 이 정적 Comparator를 제공한다.

```java
public static final Comparator <String> CASE_INSENSITIVE_ORDER
```

생성자에서 제공할 수 있다.

```java
Map<String, Integer> treeMap = new TreeMap<>(String.CASE_INSENSITIVE_ORDER);
treeMap.put("abc", 1);
treeMap.put("ABC", 2);
```

테스트를 실행하면 맵의 크기가 1임을 알 수 있다.

```java
assertEquals(1, treeMap.size());
```

값이 2로 업데이트된다.

```java
assertEquals(2, treeMap.get("aBc").intValue());
assertEquals(2, treeMap.get("ABc").intValue());
```

이제 동일한 String을 사용 하지만 다른 경우를 사용하여 항목을 제거해 본다.

```java
treeMap.remove("aBC");
assertEquals(0, treeMap.size());
```

`O(1)` 삽입 및 조회를 제공하는 HashMap과 비교하여 TreeMap에 대해 `O(log n)`의 평균 시간이 소요된다.

TreeMap이 null 키를 허용하지 않는다.

## 3. Apache의 CaseInsensitiveMap
Apache의 Commons-Collections는 매우 인기 있는 Java 라이브러리로, 그 중 CaseInsensitiveMap과 함께 많은 수의 유용한 클래스를 제공한다.

**CaseInsensitiveMap은 키를 추가하거나 검색하기 전에 소문자로 변환하는 hash-based Map 이다.** TreeMap과 달리 CaseInsensitiveMap은  null 키 삽입을 허용한다.

commons-collections4 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.4</version>
</dependency>
```

CaseInsensitiveMap을 사용 하고 두 항목을 추가할 수 있다.

```java
Map<String, Integer> commonsHashMap = new CaseInsensitiveMap<>();
commonsHashMap.put("abc", 1);
commonsHashMap.put("ABC", 2);
```

테스트할 때 이전과 동일한 결과를 기대한다.

```java
assertEquals(1, commonsHashMap.size());
assertEquals(2, commonsHashMap.get("aBc").intValue());
assertEquals(2, commonsHashMap.get("ABc").intValue());

commonsHashMap.remove("aBC");
assertEquals(0, commonsHashMap.size());
```

## 4. Spring의 LinkedCaseInsensitiveMap
Spring Core는 LinkedCaseInsensitiveMap을 비롯한 유틸리티 클래스도 제공하는 Spring Framework 모듈이다.

LinkedCaseInsensitiveMap은 해시 테이블과 연결된 목록을 기반으로 하는 Map인 LinkedHashMap을 래핑한다. LinkedHashMap과 달리 null 키 삽입을 허용하지 않는다. **LinkedCaseInsensitiveMap은 원래 순서와 키의 원래 대소문자를 유지하면서 어떤 경우에도 get 및 remove와 같은 함수 호출을 허용한다.**

먼저 스프링 코어 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>
```

LinkedCaseInsensitiveMap을 초기화할 수 있다.

```java
Map<String, Integer> linkedHashMap = new LinkedCaseInsensitiveMap<>();
linkedHashMap.put("abc", 1);
linkedHashMap.put("ABC", 2);
```

테스트를 추가한다.

```java
assertEquals(1, linkedHashMap.size());
assertEquals(2, linkedHashMap.get("aBc").intValue());
assertEquals(2, linkedHashMap.get("ABc").intValue());

linkedHashMap.remove("aBC");
assertEquals(0, linkedHashMap.size());
```

## [출처 및 참고]
* [https://www.baeldung.com/java-map-with-case-insensitive-keys](https://www.baeldung.com/java-map-with-case-insensitive-keys)
