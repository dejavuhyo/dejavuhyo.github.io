---
title: Java CharSequence와 String
author: dejavuhyo
date: 2022-01-10 09:45:00 +0900
categories: [Language, Java]
tags: [java-char-sequence-string, char-sequence-string, java-char-sequence, java-string, 자바-char-sequence, 자바-string, 문자열]
---

## 1. CharSequence
CharSequence는 일련의 문자를 나타내는 인터페이스이다. 이 인터페이스는 변경성을 적용하지 않는다. 따라서 변경 가능한 클래스와 변경할 수 없는 클래스 모두 이 인터페이스를 구현한다.

물론 인터페이스는 직접 인스턴스화할 수 없다. 변수를 인스턴스화하려면 구현이 필요하다.

```java
CharSequence charSequence = "baeldung";
```

여기서 charSequence는 String으로 인스턴스화된다. 다른 인스턴스화이다.

```java
CharSequence charSequence = new StringBuffer("baeldung");
CharSequence charSequence = new StringBuilder("baeldung");
```

## 2. String
String은 Java에서 일련의 문자이다. 이는 변경할 수 없는 클래스이며 Java에서 가장 자주 사용되는 유형 중 하나이다. 이 클래스는 `CharSequence`, `Serializable` 및 `Comparable<String>` 인터페이스를 구현한다.

두 인스턴스화 아래에서 동일한 내용을 가진 문자열을 만든다. 그러나 서로 같지 않다.

```java
@Test
public void givenUsingString_whenInstantiatingString_thenWrong() {
    CharSequence firstString = "baeldung";
    String secondString = "baeldung";

    assertNotEquals(firstString, secondString);
}
```

## 3. CharSequence vs. String
CharSequence와 String의 차이점과 공통점을 비교한다. 둘 다 java.lang이라는 동일한 패키지에 있다. 그러나 전자는 인터페이스이고 후자는 구체 클래스이다. 또한 String 클래스는 변경할 수 없다.

다음 예에서 각 합계 연산은 다른 인스턴스를 생성하고 저장된 데이터의 양을 늘리며 가장 최근에 생성된 문자열을 반환한다.

```java
@Test
public void givenString_whenAppended_thenUnmodified() {
    String test = "a";
    int firstAddressOfTest = System.identityHashCode(test);
    test += "b";
    int secondAddressOfTest = System.identityHashCode(test);

    assertNotEquals(firstAddressOfTest, secondAddressOfTest);
}
```

반면에 StringBuilder는 새 값을 유지하기 위해 이미 생성된 String을 업데이트한다.

```java
@Test
public void givenStringBuilder_whenAppended_thenModified() {
    StringBuilder test = new StringBuilder();
    test.append("a");
    int firstAddressOfTest = System.identityHashCode(test);
    test.append("b");
    int secondAddressOfTest = System.identityHashCode(test);

    assertEquals(firstAddressOfTest, secondAddressOfTest);
}
```

또 다른 차이점은 인터페이스가 내장된 비교 전략을 의미하지 않는 반면 String 클래스는 `Comparable<String>` 인터페이스를 구현한다는 것 이다.

두 개의 CharSequence를 비교하려면 String으로 캐스팅 한 다음 나중에 비교할 수 있다.

> 일반적으로 char 시퀀스에 무엇을 사용해야 할지 잘 모르는 곳에서 String 을 사용한다. 그러나 경우에 따라 StringBuilder 및 StringBuffer가 더 적절할 수 있다.

> [CharSequence](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/CharSequence.html) 및 [String](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html)에 대한 자세한 정보는 JavaDocs에서 찾을 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-char-sequence-string](https://www.baeldung.com/java-char-sequence-string)
