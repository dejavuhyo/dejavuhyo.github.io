---
title: Java 순서를 무시한 두 List의 Equality
author: dejavuhyo
date: 2023-03-15 09:35:00 +0900
categories: [Language, Java]
tags: [java-list, java-lists-equality, lists-equality, java-assert-lists, 리스트-동등성, 자바-리스트]
---

## 1. 설정
[List#equals](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/List.html#equals(java.lang.Object)) Java 문서에 따라 두 List가 동일한 순서로 동일한 요소를 포함하는 경우 동일하다. 따라서 우리는 순서에 구애받지 않는 비교를 원하기 때문에 단순히 equals 메서드를 사용할 수 없다.

다음 세 가지 list를 테스트를 위한 예제 입력으로 사용한다.

```java
List first = Arrays.asList(1, 3, 4, 6, 8);
List second = Arrays.asList(8, 1, 6, 3, 4);
List third = Arrays.asList(1, 3, 3, 6, 6);
```

## 2. JUnit 사용
JUnit은 Java 생태계에서 단위 테스트에 사용되는 잘 알려진 프레임워크이다.

아래 논리를 사용하여 `assertTrue` 및 `assertFalse` 메서드를 사용하여 두 List의 동등성을 비교할 수 있다.

여기에서 두 List의 크기를 확인하고 첫 번째 List가 두 번째 List의 모든 요소를 ​​포함하는지 또는 그 반대인지 확인한다. 이 솔루션은 작동하지만 읽기가 쉽지 않다. 이제 몇 가지 대안이 있다.

```java
@Test
public void whenTestingForOrderAgnosticEquality_ShouldBeTrue() {
    assertTrue(first.size() == second.size() && first.containsAll(second) && second.containsAll(first));
}
```

이 첫 번째 테스트에서는 두 List의 요소가 동일한지 확인하기 전에 두 List의 크기를 비교한다. 이 두 조건이 모두 true를 반환하면 테스트가 통과된다.

이제 실패한 테스트이다.

```java
@Test
public void whenTestingForOrderAgnosticEquality_ShouldBeFalse() {
    assertFalse(first.size() == third.size() && first.containsAll(third) && third.containsAll(first));
}
```

대조적으로 이 버전의 테스트에서는 두 List의 크기가 동일하더라도 모든 요소가 일치하지 않다.

## 3. AssertJ 사용
AssertJ는 Java 테스트에서 유창하고 풍부한 어설션을 작성하기 위한 오픈 소스 커뮤니티 기반 라이브러리이다.

Maven 프로젝트에서 사용하기 위해 `pom.xml` 파일에 `assertj-core` 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.assertj</groupId>
    <artifactId>assertj-core</artifactId>
    <version>3.16.1</version>
</dependency>
```

동일한 요소와 크기의 두 List 인스턴스가 같은지 비교하는 테스트를 작성한다.

```java
@Test
void whenTestingForOrderAgnosticEqualityBothList_ShouldBeEqual() {
    assertThat(first).hasSameElementsAs(second);
}
```

이 예제에서 먼저 주어진 iterable의 모든 요소가 순서와 관계없이 포함되어 있는지 확인한다. 이 접근 방식의 주요 제한 사항은 `hasSameElementsAs` 메서드가 중복 항목을 무시한다는 것이다.

```java
@Test
void whenTestingForOrderAgnosticEqualityBothList_ShouldNotBeEqual() {
    List a = Arrays.asList("a", "a", "b", "c");
    List b = Arrays.asList("a", "b", "c");
    assertThat(a).hasSameElementsAs(b);
}
```

이 테스트에서는 요소가 동일하지만 두 List의 크기가 같지 않지만 중복 항목을 무시하므로 어설션은 여전히 ​​참이다.

`hasSameElementsAs()`와 관련된 문제를 극복하기 위해 `containsExactlyInAnyOrderElementsOf()`를 사용할 수 있다. 이 기능은 두 List에 순서에 관계없이 정확히 동일한 요소가 포함되어 있는지 확인한다.

```java
assertThat(a).containsExactlyInAnyOrderElementsOf(b);
```

테스트는 실제로 예상대로 실패한다.

## 4. Hamcrest 사용
이미 Hamcrest를 사용 중이거나 단위 테스트 작성에 사용하려는 경우 순서에 구애받지 않는 비교를 위해 `Matchers#containsInAnyOrder` 메서드를 사용하는 방법은 다음과 같다.

Maven 프로젝트에서 Hamcrest를 사용하기 위해 `pom.xml` 파일에 `hamcrest-all` 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.hamcrest</groupId>
    <artifactId>hamcrest-all</artifactId>
    <version>1.3</version>
</dependency>
```

테스트를 살펴본다.

```java
@Test
public void whenTestingForOrderAgnosticEquality_ShouldBeEqual() {
    assertThat(first, Matchers.containsInAnyOrder(second.toArray()));
}
```

여기서 `containsInAnyOrder` 메소드는 Iterables에 대해 검사된 Iterable 요소와 일치하는 순서에 구애받지 않는 매처를 생성한다. 이 테스트는 List의 요소 순서를 무시하고 두 List의 요소를 일치시킨다.

이 솔루션은 이전 섹션에서 설명한 것과 동일한 문제를 겪지 않으므로 명시적으로 크기를 비교할 필요가 없다.

## 5. Apache Commons 사용
JUnit, Hamcrest 또는 AssertJ 외에 또 다른 라이브러리 또는 프레임워크는 Apache CollectionUtils 이다. 광범위한 사용 사례를 포괄하는 일반적인 작업에 대한 유틸리티 메서드를 제공하고 상용구 코드 작성을 방지하는 데 도움이 된다.

Maven 프로젝트에서 사용하기 위해 `pom.xml` 파일에 `commons-collections4` 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.4</version>
</dependency>
```

다음은 CollectionUtils를 사용한 테스트이다.

```java
@Test
public void whenTestingForOrderAgnosticEquality_ShouldBeTrueIfEqualOtherwiseFalse() {
    assertTrue(CollectionUtils.isEqualCollection(first, second));
    assertFalse(CollectionUtils.isEqualCollection(first, third));
}
```

`isEqualCollection` 메소드는 주어진 컬렉션이 동일한 카디널리티를 가진 정확히 동일한 요소를 포함하는 경우 `true`를 반환한다. 그렇지 않으면 `false`를 반환한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-assert-lists-equality-ignore-order](https://www.baeldung.com/java-assert-lists-equality-ignore-order)
