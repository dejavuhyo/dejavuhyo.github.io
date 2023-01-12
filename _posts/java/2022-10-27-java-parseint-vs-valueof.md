---
title: Java parseInt()와 valueOf()의 차이점
author: dejavuhyo
date: 2022-10-27 16:00:00 +0900
categories: [Language, Java]
tags: [java-parseint, java-valueof, java-parseint-valueof, parseint-valueof-차이점, 자바-parseint, 자바-valueof]
---

## 1. parseInt() 메서드
java.lang.Integer 클래스는 `parseInt()` 메소드의 세 가지 변형을 제공한다.

### 1) 문자열 을 정수로 변환
`parseInt()`의 첫 번째 변형은 String을 매개변수로 받아들이고 기본 데이터 유형 int를 반환한다. String을 정수로 변환할 수 없을 때 NumberFormatException이 발생한다.

```java
public static int parseInt(String s) throws NumberFormatException
```

이제 문자열에서 정수로의 구문 분석이 어떻게 발생하는지 이해하기 위해 부호 있는/부호 없는 숫자 문자열을 매개변수로 전달하는 예이다.

```java
@Test
public void whenValidNumericStringIsPassed_thenShouldConvertToPrimitiveInt() {
    assertEquals(11, Integer.parseInt("11")); 
    assertEquals(11, Integer.parseInt("+11")); 
    assertEquals(-11, Integer.parseInt("-11"));
}
```

### 2) 기수 지정
`parseInt()` 메서드의 두 번째 변형은 String과 int를 매개변수로 받아들이고 기본 데이터 유형 int를 반환한다. 첫 번째 특징과 마찬가지로 String을 정수로 변환할 수 없을 때 NumberFormatException이 발생한다.

```java
public static int parseInt(String s, int radix) throws NumberFormatException
```

기본적으로 `parseInt()` 메서드는 주어진 문자열이 10진수 정수라고 가정한다. 여기에서 매개변수 radix는 문자열을 정수로 변환하는데 사용되는 radix 또는 기저이다.

이것을 더 잘 이해하기 위해 `parseInt()`에 radix 매개변수와 함께 문자열을 전달하는 몇 가지 예를 살펴본다.

```java
@Test
public void whenValidNumericStringWithRadixIsPassed_thenShouldConvertToPrimitiveInt() {
    assertEquals(17, Integer.parseInt("11", 16));
    assertEquals(10, Integer.parseInt("A", 16)); 
    assertEquals(7, Integer.parseInt("7", 8));
}
```

이제 기수를 사용한 문자열 변환이 어떻게 발생하는지 확인한다. 예를 들어, 기수가 13인 숫자 시스템에서 398과 같은 숫자 문자열은 10진수(with radix/base 10) 632를 나타낸다.

마찬가지로 위의 예에서 Integer.parseInt("11", 16)은 계산 시 17을 반환했다.

### 3) 부분 문자열을 정수로 변환
`parseInt()` 메서드의 세 번째 변형은 CharSequence, 하위 문자열의 두 정수 beginIndex와 endIndex, 다른 정수 radix를 매개변수로 받는다. 잘못된 문자열이 전달되면 NumberFormatException이 발생한다.

```java
public static int parseInt(CharSequence s, int beginIndex, int endIndex, int radix) throws NumberFormatException
```

JDK 9는 Integer 클래스에 이 정적 메서드를 도입했다.

```java
@Test
public void whenValidNumericStringWithRadixAndSubstringIsPassed_thenShouldConvertToPrimitiveInt() {
    assertEquals(5, Integer.parseInt("100101", 3, 6, 2));
    assertEquals(101, Integer.parseInt("100101", 3, 6, 10));
}
```

주어진 기수를 사용하여 부분 문자열을 정수로 변환하는 방법을 확인한다. 여기서 string은 "100101"이고 beginIndex와 endIndex는 각각 3과 6이다. 따라서 부분 문자열은 "101"이다. expectedNumber1의 경우 전달된 기수는 2이며, 이는 바이너리임을 의미한다.

따라서 부분 문자열 "101"은 정수 5로 변환된다. 또한 expectedNumber2에 대해 전달된 기수는 10이며 이는 십진수임을 의미한다. 결과적으로 부분 문자열 "101"은 정수 101로 변환된다.

또한 잘못된 문자열이 전달될 때 Integer.parseInt() 가 NumberFormatException을 발생시키는 것을 볼 수 있다.

```java
@Test(expected = NumberFormatException.class)
public void whenInValidNumericStringIsPassed_thenShouldThrowNumberFormatException(){
    int number = Integer.parseInt("abcd");
}
```

## 2. valueOf() 메서드
java.lang.Integer 클래스에서 제공하는 `valueOf()` 메소드의 세 가지 변형이다.

### 1) 문자열을 정수로 변환
`valueOf()` 메서드의 첫 번째 변형은 String을 매개 변수로 받아들이고 래퍼 클래스 Integer를 반환한다. 숫자가 아닌 문자열이 전달되면 NumberFormatException이 발생한다.

```java
public static Integer valueOf(String s) throws NumberFormatException
```

구현 시 parseInt(String s, int radix)를 사용한다.

다음으로 부호 있는/부호 없는 숫자 문자열을 정수로 변환하는 몇 가지 예이다.

```java
@Test
public void whenValidNumericStringIsPassed_thenShouldConvertToInteger() {
    Integer expectedNumber = 11;
    Integer expectedNegativeNumber = -11;
        
    assertEquals(expectedNumber, Integer.valueOf("11"));
    assertEquals(expectedNumber, Integer.valueOf("+11"));
    assertEquals(expectedNegativeNumber, Integer.valueOf("-11"));
}
```

### 2) int를 정수로 변환
`valueOf()`의 두 번째 변형은 int를 매개변수로 받아들이고 래퍼 클래스 Integer를 반환한다. 또한 float과 같은 다른 데이터 유형이 전달되면 컴파일 시간 오류가 생성된다.

```java
public static Integer valueOf(int i)
```

int를 Integer로 변환하는 것 외에도 이 메서드는 char를 매개 변수로 허용하고 해당 유니코드 값을 반환할 수도 있다.

이것을 더 이해하기 위한 예이다.

```java
@Test
public void whenNumberIsPassed_thenShouldConvertToInteger() {
    Integer expectedNumber = 11;
    Integer expectedNegativeNumber = -11;
    Integer expectedUnicodeValue = 65;
        
    assertEquals(expectedNumber, Integer.valueOf(11));
    assertEquals(expectedNumber, Integer.valueOf(+11));
    assertEquals(expectedNegativeNumber, Integer.valueOf(-11));
    assertEquals(expectedUnicodeValue, Integer.valueOf('A'));
}
```

### 3) 기수 지정
`valueOf()`의 세 번째 변형은 String과 int를 매개변수로 받아들이고 래퍼 클래스 Integer를 반환한다. 또한 우리가 본 다른 모든 변형과 마찬가지로 주어진 문자열을 Integer 유형으로 변환할 수 없을 때 NumberFormatException이 발생한다.

```java
public static Integer valueOf(String s, int radix) throws NumberFormatException
```

이 메서드는 구현 시 parseInt(String s, int radix)도 사용한다.

기본적으로 `valueOf()` 메서드는 주어진 String이 10진법 정수를 나타낸다고 가정한다. 또한 이 메서드는 기본 radix를 변경하는 또 다른 인수를 허용한다.

몇 가지 String 개체를 구문 분석해 본다.

```java
@Test
public void whenValidNumericStringWithRadixIsPassed_thenShouldConvertToInetger() {
    Integer expectedNumber1 = 17;
    Integer expectedNumber2 = 10;
    Integer expectedNumber3 = 7;
        
    assertEquals(expectedNumber1, Integer.valueOf("11", 16));
    assertEquals(expectedNumber2, Integer.valueOf("A", 16));
    assertEquals(expectedNumber3, Integer.valueOf("7", 8));
}
```

## 3. parseInt()와 valueOf()의 차이점
다음은 `valueOf()` 메서드와 `parseInt()` 메서드의 주요 차이점 이다.

| `Integer.valueOf()` | `Integer.parseInt()` |
|:-----:|:-----:|
| Integer 객체를 반환한다. | 원시 int를 반환한다. |
| 이 메서드는 String과 int를 매개변수로 받아들인다. | 이 메서드는 문자열만 매개변수로 받아들인다. |
| 메서드 구현에서 `Integer.parseInt()`를 사용 한다. | 문자열을 정수로 구문 분석하는 데 도우미 메서드를 사용하지 않는다. |
| 이 메서드는 문자를 매개 변수로 받아들이고 해당 유니코드 값을 반환한다. | 이 메소드는 문자를 매개 변수로 전달할 때 호환되지 않는 유형 오류를 생성한다. |

## [출처 및 참고]
* [https://www.baeldung.com/java-integer-parseint-vs-valueof](https://www.baeldung.com/java-integer-parseint-vs-valueof)
