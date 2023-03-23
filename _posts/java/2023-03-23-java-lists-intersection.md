---
title: Java 두 List의 교집합
author: dejavuhyo
date: 2023-03-23 09:00:00 +0900
categories: [Language, Java]
tags: [java-list, java-list-intersection, list-intersection, 자바-교집합, 자바-리스트-교집합, 리스트-교집합]
---

## 1. 두 문자열 List의 교집합
일부 중복된 요소가 있는 두 개의 Strings Lists를 교집합으로 만든다.

```java
List<String> list = Arrays.asList("red", "blue", "blue", "green", "red");
List<String> otherList = Arrays.asList("red", "green", "green", "yellow");
```

이제 스트림 메서드를 사용하여 목록의 교차점을 결정한다.

```java
Set<String> result = list.stream()
  .distinct()
  .filter(otherList::contains)
  .collect(Collectors.toSet());

Set<String> commonElements = new HashSet(Arrays.asList("red", "green"));

Assert.assertEquals(commonElements, result);
```

먼저, distinct로 중복된 요소를 제거한다. 그런 다음 필터를 사용하여 otherList에도 포함된 요소를 선택한다.

마지막으로 출력을 Collector로 변환한다. 교차는 각 공통 요소를 한 번만 포함해야 한다. 순서는 중요하지 않으므로 `toSet`이 가장 간단한 선택이지만 `toList` 또는 다른 수집기 메서드를 사용할 수도 있다.

## 2. 사용자 지정 클래스 목록의 교차점
List가 String을 포함하지 않고 커스텀 클래스의 인스턴스를 포함하면 Java의 규칙을 따르는 한 스트림 메서드를 사용하는 솔루션은 사용자 정의 클래스에 대해 잘 작동할 것이다.

contains 메서드는 특정 개체가 목록에 표시되는지 여부를 결정하는 방법은 equals 메서드를 재정의하고 관련 속성값을 기반으로 두 개체를 비교하는지 확인해야 한다.

예를 들어 너비와 높이가 같으면 두 개의 사각형이 같다.

equals 메서드를 재정의하지 않으면 클래스는 부모 클래스의 equals 구현을 사용한다. 결국 상속 체인인 Object 클래스의 equals 메서드가 실행된다. 그런 다음 두 인스턴스는 힙에서 정확히 동일한 개체를 참조하는 경우에만 동일하다.

## [출처 및 참고]
* [https://www.baeldung.com/java-lists-intersection](https://www.baeldung.com/java-lists-intersection)
