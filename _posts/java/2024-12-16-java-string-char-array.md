---
title: Java String을 Char Array로 변환
author: dejavuhyo
date: 2024-12-16 09:25:00 +0900
categories: [Language, Java]
tags: [java-string, string-char-array, char-array, 자바-문자-배열, 자바-배열]
---

## 1. for 루프 사용
String characters를 하나씩 가져오는 코드를 직접 작성할 수 있다. 그런 다음 문자 배열을 문자로 채운다.

* 예제

```java
import java.util.Arrays;

public class StringChar {
    public static void main(String[] args) {
        String s = "abc123";
        char[] chrs = new char[s.length()];
        for (int i = 0; i < s.length(); i++) {
            chrs[i] = s.charAt(i);
        }
        System.out.println(Arrays.toString(chrs));
    }
}
```

* 결과

```text
[a, b, c, 1, 2, 3]
```

이 방법은 char 배열을 채우기 위해 추가 논리를 원할 때 유용하다. 예를 들어, 중복 문자를 건너뛰거나 소문자로 변경하는 등의 방법이다.

## 2. toCharArray() 메서드
이것은 문자열을 char 배열로 변환하는 권장 방법이다. `toCharArray()` 메서드를 사용하여 문자열을 char 배열로 변환하는 예제이다.

* 예제

```java
import java.util.Arrays;

public class StringChar {
    public static void main(String[] args) {
        String s = "abc123";
        char[] chars = s.toCharArray();
        System.out.println(Arrays.toString(chars));
    }
}
```

* 결과

```text
[a, b, c, 1, 2, 3]
```

## 3. charAt() 메서드
때때로 특정 인덱스에서 문자를 가져와야 한다. 이를 위해 `charAt()` 메서드를 사용할 수 있다.

* 예제

```java
public class StringChar {
    public static void main(String[] args) {
        String s = "Java";
        System.out.println(s);
        char c = s.charAt(3);
        System.out.println(c);
    }
}
```

* 결과

```text
Java
a
```

## 4. getChars() 메서드
이 메서드는 특정 인덱스 간에 문자를 복사할 때 유용하다. String `getChars()` 메서드는 문자열에서 대상 문자 배열로 문자를 복사gks다. 메서드 구문은 다음과 같다.

* 메서드 구문

```java
public void getChars(int srcBegin, int srcEnd, char dst[], int dstBegin)
```

* srcBegin: 문자가 복사될 인덱스이다.

* srcEnd: 복사를 멈출 인덱스이다. 복사할 마지막 인덱스는 srcEnd-1이다.

* dst: 대상 문자 배열이다. 이 메서드를 호출하기 전에 초기화해야 한다.

* dstBegin: 대상 배열의 시작 인덱스이다.

* 예제

```java
import java.util.Arrays;

public class StringChar {
    public static void main(String[] args) {
        String s = "abc123";
        char[] substringChars = new char[7];
        s.getChars(1, 4, substringChars, 2);
        System.out.println(Arrays.toString(substringChars));
    }
}
```

* 결과

```text
[ ,  , b, c, 1,  ,  ]
```

문자열 문자는 인덱스 1부터 3까지 복사된다. 문자는 대상 배열의 2번째 인덱스부터 복사된다.

대상 배열 크기가 필요한 크기보다 작으면 `java.lang.StringIndexOutOfBoundsExceptionthrow`이 된다.

* 예제

```java
import java.util.Arrays;

public class StringChar {
    public static void main(String[] args) {
        String s = "abc123";
        char[] substringChars = new char[2];
        s.getChars(1, 4, substringChars, 2);
        System.out.println(Arrays.toString(substringChars));
    }
}
```

* 결과

```text
Exception in thread "main" java.lang.StringIndexOutOfBoundsException: offset 2, count 3, length 2
	at java.base/java.lang.String.checkBoundsOffCount(String.java:4591)
	at java.base/java.lang.String.getChars(String.java:1679)
```

## [출처 및 참고]
* [https://www.javastring.net/java/string/convert-string-char-array-java](https://www.javastring.net/java/string/convert-string-char-array-java)
