---
title: Java 두 개의 List 비교
author: dejavuhyo
date: 2023-03-07 09:30:00 +0900
categories: [Language, Java]
tags: [java-list, list-equals, java-assert, list-ordinality-equality, 자바-리스트, 리스트-비교, 자바-리스트-비교]
---

## 1. 두 개의 List 비교
List는 정렬 된 데이터 구조이므로 요소의 순서는 의도적으로 중요하다.

상세 사항은 [List#equals](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/List.html#equals(java.lang.Object))를 참고한다.

> 두 개의 List는 동일한 요소를 동일한 순서로 포함하는 경우 동일한 것으로 정의된다.

이 정의는 `equals` 메소드가 List 인터페이스의 다른 구현에서 제대로 작동하도록 한다.

다음 코드에서는 다음 목록을 예제 입력으로 사용한다.

```java
List<String> list1 = Arrays.asList("1", "2", "3", "4");
List<String> list2 = Arrays.asList("1", "2", "3", "4");
List<String> list3 = Arrays.asList("1", "2", "4", "3");
```

## 2. JUnit
JUnit 테스트에서 다음 어설션이 참이다.

```java
@Test
public void whenTestingForEquality_ShouldBeEqual() throws Exception {
    Assert.assertEquals(list1, list2);
    Assert.assertNotSame(list1, list2);
    Assert.assertNotEquals(list1, list3);
}
```

## 3. TestNG
TestNG의 어설션을 사용할 때 JUnit의 어설션과 매우 유사하게 보이지만 [Assert](https://www.javadoc.io/doc/org.testng/testng/6.9.5/org/testng/Assert.html) 클래스가 다른 패키지에서 온다.

```java
@Test
public void whenTestingForEquality_ShouldBeEqual() throws Exception {
    Assert.assertEquals(list1, list2);
    Assert.assertNotSame(list1, list2);
    Assert.assertNotEquals(list1, list3);
}
```

## 4. AssertJ
[AssertJ](joel-costigliola.github.io/assertj/)를 사용하려는 경우 어설션은 다음과 같다.

```java
@Test
public void whenTestingForEquality_ShouldBeEqual() throws Exception {
    assertThat(list1)
      .isEqualTo(list2)
      .isNotEqualTo(list3);

    assertThat(list1.equals(list2)).isTrue();
    assertThat(list1.equals(list3)).isFalse();
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-test-a-list-for-ordinality-and-equality](https://www.baeldung.com/java-test-a-list-for-ordinality-and-equality)
