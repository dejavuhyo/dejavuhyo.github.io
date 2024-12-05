---
title: Java String repeat()
author: dejavuhyo
date: 2024-12-05 09:13:00 +0900
categories: [Language, Java]
tags: [java-string-repeat, string-repeat, 자바-문자열-반복, 문자열-반복]
---

## 1. repeat()
`repeat()` 메서드는 이 문자열을 주어진 횟수만큼 연결한 값을 갖는 새로운 문자열을 반환한다.

## 2. repeat(int count) 중요점

* count가 0이면, 빈 문자열이 반환된다.

* count가 음수이면, `IllegalArgumentException`이 throw 된다.

* count가 1이면, 이 문자열에 대한 참조가 반환된다.

* 이 문자열이 비어 있으면 빈 문자열이 반환된다.

* 이 유틸리티 메서드는 Java 11 릴리스의 String 클래스에 추가되었다.

* 이 메서드는 내부적으로 `Arrays.fill()` 및 `System.arraycopy()` 메서드를 호출하여 새 문자열을 생성한다.

## 3. 예제

### 1) 문자열 반복

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello, World!";
        String result = str.repeat(3);
        System.out.println(result);
    }
}
```

* 결과

```text
Hello, World!Hello, World!Hello, World!
```

### 2) count가 음수인 경우

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello, World!";
        String result = str.repeat(-1);
        System.out.println(result);
    }
}
```

* 결과

```text
Exception in thread "main" java.lang.IllegalArgumentException: count is negative: -1
	at java.base/java.lang.String.repeat(String.java:4417)
```

### 3) count가 0인 경우

* 예제

```java

```

* 결과

```text

```

### 4) 문자열이 비어 있는 경우

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello, World!";
        String result = str.repeat(0);
        System.out.println(result);
    }
}
```

* 결과

```text

```

###  5) count운트가 1인 경우

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello, World!";
        String result = str.repeat(1);
        System.out.println(result);
    }
}
```

* 결과

```text
Hello, World!
```

## 4. OutOfMemoryError
결과 문자열 길이가 너무 크면 `OutOfMemoryError`를 throw 한다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello, World!";
        String result = str.repeat(Integer.MAX_VALUE);
        System.out.println(result);
    }
}
```

* 결과

```text
Exception in thread "main" java.lang.OutOfMemoryError: Required length exceeds implementation limit
	at java.base/java.lang.String.repeat(String.java:4427)
```

## [출처 및 참고]
* [https://www.javastring.net/java/string/java-string-repeat-method](https://www.javastring.net/java/string/java-string-repeat-method)
