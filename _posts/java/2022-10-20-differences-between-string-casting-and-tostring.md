---
title: Java (String)과 .toString() 차이점
author: dejavuhyo
date: 2022-10-20 11:30:00 +0900
categories: [Language, Java]
tags: [java-string-casting, string-casting, java-tostring, tostring, string-casting-tostring, differences-string-casting-tostring]
---

## 1. String 타입 Casting과 toString() 메소드
`(String)` 구문을 사용하는 것은 Java의 유형 캐스팅과 엄격하게 연결된다. 간단히 말해서 이 구문을 사용하는 주요 작업은 소스 변수를 String으로 캐스팅하는 것이다.

```java
String str = (String) object;
```

Java의 모든 클래스는 직접 또는 간접적으로 `toString()` 메서드를 구현하는 Object 클래스의 확장이다. 우리는 그것을 사용하여 모든 Object의 String 표현을 얻는다.

```java
String str = object.toString();
```

## 2. (String) vs toString()
각 접근 방식을 언제 사용해야 하는지 이해하는 데 도움이 되는 몇 가지 예이다.

Object 변수가 있고 String을 얻으려고 한다. 유의 사항은 아래 유틸리티 메서드는 주제를 설명하는 데만 사용된다. 실제로는 이와 같은 유틸리티 메서드를 사용하지 않는다.

먼저 Object를 String으로 캐스팅하는 간단한 유틸리티 메서드이다.

```java
public static String castToString(Object object) {
    if (object instanceof String) {
        return (String) object;
    }
    return null;
}
```

캐스팅하기 전에 객체 변수가 String의 인스턴스인지 확인해야 한다. 그렇지 않으면 실패하고 ClassCastException을 생성할 수 있다.

```java
@Test(expected = ClassCastException.class)
public void givenIntegerObject_whenCastToObjectAndString_thenCastClassException() {
    Integer input = 1234;

    Object obj = input;
    String str = (String) obj;
}
```

그러나 이 작업은 null로부터 안전하다. 이전에 String 변수에 적용한 적이 없더라도 인스턴스화되지 않은 변수에 사용하면 성공한다.

```java
@Test
public void givenNullInteger_whenCastToObjectAndString_thenSameAndNoException() {
    Integer input = null;

    Object obj = input;
    String str = (String) obj;

    assertEquals(obj, str);
    assertEquals(str, input);
    assertSame(input, str);
}
```

요청된 객체에서 `toString()`을 호출하는 다른 유틸리티 함수를 구현한다.

```java
public static String getStringRepresentation(Object object) {
    if (object != null) {
        return object.toString();
    }
    return null;
}
```

이 경우 객체의 유형을 알 필요가 없으며 typecasting 없이 객체에서 성공적으로 실행할 수 있다. 간단한 null 검사만 추가하면 된다. 이 검사를 추가하지 않으면 인스턴스화되지 않은 변수를 메서드에 전달할 때 NullPointerException이 발생할 수 있다.

```java
@Test(expected = NullPointerException.class)
public void givenNullInteger_whenToString_thenNullPointerException() {
    Integer input = null;

    String str = input.toString();
}
```

또한 핵심 String 구현으로 인해 String 변수에서 `toString()` 메서드를 실행하면 동일한 객체가 반환된다.

```java
@Test
public void givenString_whenToString_thenSame() {
    String str = "baeldung";

    assertEquals("baeldung", str.toString());
    assertSame(str, str.toString());
}
```

> 변수가 String 인스턴스라는 것을 알고 있으면 type 캐스팅을 사용해야 한다.

```java
@Test
public void givenString_whenCastToObject_thenCastToStringReturnsSame() {
    String input = "baeldung";
    
    Object obj = input;
    
    assertSame(input, StringCastUtils.castToString(obj));
}
```

이 접근 방식은 일반적으로 추가 함수 호출을 수행할 필요가 없으므로 더 효율적이고 빠르다. 그러나 String을 Object로 전달해서는 안 된다.

> 다른 객체 유형을 전달할 때 `toString()` 메서드를 명시적으로 호출해야 한다. 구현에 따라 String 값을 반환한다.

```java
@Test
public void givenIntegerNotNull_whenCastToObject_thenGetToStringReturnsString() {
    Integer input = 1234;

    Object obj = input;

    assertEquals("1234", StringCastUtils.getStringRepresentation(obj));
    assertNotSame("1234", StringCastUtils.getStringRepresentation(obj));
}
```

## [출처 및 참고]
* <https://www.baeldung.com/java-string-casting-vs-tostring>
