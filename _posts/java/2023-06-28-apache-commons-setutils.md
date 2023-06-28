---
title: Apache Commons Collections SetUtils
author: dejavuhyo
date: 2023-06-28 09:00:00 +0900
categories: [Language, Java]
tags: [apache-commons, apache-commons-setutils, setutils, 아파치-커먼즈]
---

## 1. 종속성 설치
프로젝트에서 SetUtils 라이브러리를 사용하려면 프로젝트의 `pom.xml` 파일에 다음 종속성을 추가해야 한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.1</version>
</dependency>
```

또는 프로젝트가 Gradle 기반인 경우 프로젝트의 `build.gradle` 파일에 종속성을 추가해야 한다. 또한 `build.gradle` 파일의 저장소 섹션에 `mavenCentral()`을 추가해야 한다.

```text
compile 'org.apache.commons:commons-collections4:4.1'
```

## 2. Predicated Set
SetUtils 라이브러리의 `predicatedSet()` 메서드는 세트에 삽입될 모든 요소가 충족해야 하는 정의 조건을 허용한다. 소스 Set 개체와 술어를 허용한다.

이를 사용하여 Set의 모든 요소가 특정 조건을 충족하는지 쉽게 검증할 수 있으며, 이는 타사 라이브러리/API를 개발할 때 편리할 수 있다.

요소에 대한 유효성 검사가 실패하면 IllegalArgumentException이 발생한다. 아래 스니펫은 'L'로 시작하지 않는 문자열을 sourceSet 또는 반환된 validationSet에 추가하는 것을 방지한다.

```java
Set<String> validatingSet = SetUtils.predicatedSet(sourceSet, s -> s.startsWith("L"));
```

또한 라이브러리에는 각각 SortedSet 및 NavigableSet 작업을 위한 `predicatedSortedSet()` 및 `predicatedNavigableSet()`이 있다.

## 3. 집합의 합집합, 차집합, 교집합
라이브러리에는 집합 요소의 합집합, 차이 및 교집합을 계산할 수 있는 메서드가 있다.

`difference()` 메서드는 두 개의 Set 개체를 사용하고 변경할 수 없는 `SetUtils.SetView`를 반환한다. 반환된 `SetUtils.SetView`는 세트 a에는 있지만 세트 b에는 없는 요소를 포함한다.

```java
Set<Integer> a = new HashSet<>(Arrays.asList(1, 2, 5));
Set<Integer> b = new HashSet<>(Arrays.asList(1, 2));
SetUtils.SetView<Integer> result = SetUtils.difference(a, b);

assertTrue(result.size() == 1 && result.contains(5));
```

반환된 SetUtils에서 `add()` 또는 `addAll()`과 같은 쓰기 작업을 수행하려는 시도에 유의한다. SetView는 UnsupportedOperationException을 발생시킨다.

반환된 결과를 수정하려면 반환된 SetUtils의 `toSet()` 메서드를 호출해야 한다. 쓰기 가능한 Set 객체를 얻기 위한 SetView이다.

```java
Set<Integer> mutableSet = result.toSet();
```

SetUtils 라이브러리의 합집합 메서드는 말 그대로 작동한다. 집합 a 및 b의 모든 요소를 ​​반환한다. union 메서드는 변경할 수 없는 `SetUtil.SetView` 개체도 반환한다.

```java
Set<Integer> expected = new HashSet<>(Arrays.asList(1, 2, 5));
SetUtils.SetView<Integer> union = SetUtils.union(a, b);

assertTrue(SetUtils.isEqualSet(expected, union));
```

assert 문에 사용된 `isEqualSet()` 메서드는 두 세트가 동일한지 효과적으로 확인하는 SetUtils 라이브러리의 편리한 정적 메서드이다.

세트의 교집합, 즉 세트 a와 세트 b에 모두 존재하는 요소를 얻으려면 `SetUtils.intersection()`를 사용한다. 이 메서드는 `SetUtil.SetView` 개체도 반환한다.

```java
Set<Integer> expected = new HashSet<>(Arrays.asList(1, 2));
SetUtils.SetView<Integer> intersect = SetUtils.intersection(a, b);

assertTrue(SetUtils.isEqualSet(expected, intersect));
```

## 4. 집합 요소 변환
`SetUtils.transformedSet()` 메서드는 Set 개체와 Transformer 인터페이스를 허용한다. 소스 집합에 의해 지원되며 Transformer 인터페이스의 `transform()` 메서드를 사용하여 집합의 모든 요소를 ​​변환한다.

변환 로직은 Transformer 인터페이스의 `transform()` 메소드에 정의되어 있으며 세트에 추가된 모든 요소에 적용된다. 아래 코드 예제는 세트에 추가된 모든 요소에 2를 곱한다.

```java
Set<Integer> a = SetUtils.transformedSet(new HashSet<>(), e -> e * 2  );
a.add(2);

assertEquals(a.toArray()[0], 4);
```

`transformSet()` 메서드는 매우 편리하다. 집합의 요소를 변환하는 데 사용할 수도 있다. 예를 들어 String에서 Integer이다. 출력 유형이 입력의 하위 유형인지 확인한다.

HashSet 대신 SortedSet 또는 NavigableSet으로 작업한다고 가정한다.

새 HashSet 인스턴스가 `transformSet()` 메서드에 전달된다. 비어 있지 않은 기존 Set이 메서드에 전달되는 경우 기존 요소는 변환되지 않는다.

기존 요소(및 이후에 추가된 요소)를 변환하려면 `org.apache.commons.collections4.set.TransformedSet` 의 `transformSet()` 메서드를 사용해야 한다.

```java
Set<Integer> source = new HashSet<>(Arrays.asList(1));
Set<Integer> newSet = TransformedSet.transformedSet(source, e -> e * 2);

assertEquals(newSet.toArray()[0], 2);
assertEquals(source.toArray()[0], 2);
```

소스 세트의 요소가 변환되고 결과가 반환된 newSet에 복사된다.

## 5. 분리 설정
SetUtils 라이브러리는 집합 분리를 찾는데 사용할 수 있는 정적 메서드를 제공한다. 집합 a와 집합 b의 분리는 집합 a와 집합 b에 고유한 모든 요소이다.

SetUtils 라이브러리의 `disjunction()` 메서드를 사용하는 방법이다.

```java
Set<Integer> a = new HashSet<>(Arrays.asList(1, 2, 5));
Set<Integer> b = new HashSet<>(Arrays.asList(1, 2, 3));
SetUtils.SetView<Integer> result = SetUtils.disjunction(a, b);
 
assertTrue(result.toSet().contains(5) && result.toSet().contains(3));
```

## 6. SetUtils 라이브러리의 기타 메소드
SetUtils 라이브러리에는 설정 데이터를 쉽게 처리할 수 있는 다른 메서드가 있다.

* `thread-safe` Set을 얻기 위해 `synchronizedSet()` 또는 `synchronizedSortedSet()`을 사용할 수 있다. 그러나 비결정적 동작을 피하기 위해 반환된 세트의 반복자를 수동으로 동기화해야 한다.

* `SetUtils.unmodifiableSet()`을 사용하여 읽기 전용 세트를 얻을 수 있다. 반환된 Set Object에 요소를 추가하려고 하면 UnsupportedOperationException이 발생한다.

* 유형이 안전하고 변경할 수 없는 빈 집합을 반환하는 `SetUtils.emptySet()` 메서드도 있다.

* `SetUtils.emptyIfNull()` 메서드는 null을 허용하는 Set 개체를 허용한다. 제공된 Set이 null 이면 빈 읽기 전용 Set을 반환한다. 그렇지 않으면 제공된 세트를 반환한다.

* `SetUtils.orderedSet()`는 요소가 추가되는 순서를 유지하는 Set 객체를 반환한다.

* `SetUtils.hashCodeForSet()`은 동일한 요소의 두 세트가 동일한 해시코드를 갖는 방식으로 세트에 대한 해시코드를 생성할 수 있다.

* `SetUtils.newIdentityHashSet()`는 `==`를 사용하여 `equals()` 메서드 대신 요소를 일치시키는 HashSet를 반환한다. [주의사항](https://commons.apache.org/proper/commons-collections/apidocs/org/apache/commons/collections4/SetUtils.html#newIdentityHashSet--)을 참고한다.

## [출처 및 참고]
* [https://www.baeldung.com/apache-commons-setutils](https://www.baeldung.com/apache-commons-setutils)
