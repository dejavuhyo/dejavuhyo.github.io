---
title: Java String에서 Double로 변환
author: dejavuhyo
date: 2024-12-12 10:35:00 +0900
categories: [Language, Java]
tags: [java-string, java-double, string-double, 자바-스트링, 자바-더블, 스트링-더블]
---

## 1. Double.parseDouble()
`Double.parseDouble()`을 사용하여 String 객체를 double로 변환할 수 있다. 문자열은 "+" 또는 "-"로 시작하여 양수 및 음수 double 값을 선언할 수 있다. 문자열은 "d"로 끝나 double 값을 포함한다는 것을 나타낼 수 있다.

문자열이 null이면 이 메서드는 NullPointerException을 throw한다. 문자열이 구문 분석할 수 없으면 NumberFormatException을 throw한다.

끝에 0이 있으면 출력 double 값에서 제거된다.

`Double.parseDouble()` 메서드를 사용하여 String을 double로 변환하는 예제이다.

* 예제

```java
public class StringToDouble {
    public static void main(String[] args) {
        // positive double value
        double d1 = Double.parseDouble("+10.234");
        System.out.println(d1);

        // negative double value
        double d2 = Double.parseDouble("-23.23");
        System.out.println(d2);

        // double value ending with 0s and 'd'
        double d3 = Double.parseDouble("12.3400d");
        System.out.println(d3);
    }
}
```

* 결과

```text
10.234
-23.23
12.34
```

## 2. Double.valueOf()
이 메서드는 Double 객체를 반환한다. `parseDouble()` 메서드와 매우 유사하다. `valueOf()` 메서드를 사용하여 문자열을 Double 객체로 변환하는 몇 가지 예제이다.

* 예제

```java
public class StringToDouble {
    public static void main(String[] args) {
        double d4 = Double.valueOf("+10.234");
        System.out.println(d4);

        double d5 = Double.valueOf("-23.23");
        System.out.println(d5);

        double d6 = Double.valueOf("12.3400d");
        System.out.println(d6);
    }
}
```

* 결과

```text
10.234
-23.23
12.34
```

## 3. new Double(String s)
이 Double 클래스 생성자는 문자열 인수를 받아들이고 Double 객체로 변환한다.

이 생성자는 Java 9에서 더 이상 사용되지 않는다. Java는 문자열을 double로 변환하기 위해 `parseDouble()` 또는 `valueOf()` 메서드를 사용할 것을 제안한다.

문자열이 구문 분석할 수 없는 경우 NumberFormatException이 발생한다. 이 생성자는 `valueOf()` 메서드와 같은 방식으로 작동한다.

* 예제

```java
public class StringToDouble {
    public static void main(String[] args) {
        Double d7 = new Double("+10.234");
        System.out.println(d7);

        Double d8 = new Double("-23.23");
        System.out.println(d8);

        Double d9 = new Double("12.3400d");
        System.out.println(d9);
    }
}
```

## 4. DecimalFormat.parse()
DecimalFormat은 매우 유용한 클래스이다. `parse()` 메서드를 사용하여 포맷된 문자열을 double 값으로 변환할 수 있다. 백분율 값, 통화 값, 과학적 값을 구문 분석할 수 있다.

DecimalFormat은 특정 유형의 포매터를 얻기 위한 다양한 메서드를 제공한다. 이러한 포매터 중 일부는 Locale도 지원한다.

DecimalFormat의 `parse()`메서드는 문자열을 구문 분석할 수 없는 경우 ParseException 오류를 발생시킨다.

서식이 지정된 문자열을 double 값으로 변환하는 몇 가지 예제이다.

* 예제

```java
import java.text.DecimalFormat;
import java.text.ParseException;
import java.util.Locale;

public class StringToDouble {
    public static void main(String[] args) {
        try {
            double d10 = DecimalFormat.getNumberInstance().parse("1,23.45d").doubleValue();
            System.out.println(d10);

            double d11 = DecimalFormat.getCurrencyInstance(Locale.US).parse("$12,345.68").doubleValue();
            System.out.println(d11);

        } catch (ParseException e) {
            e.printStackTrace();
        }
    }
}
```

* 결과

```text
123.45
12345.68
```

## [출처 및 참고]
* [https://www.javastring.net/java/string/string-to-double-conversion](https://www.javastring.net/java/string/string-to-double-conversion)
