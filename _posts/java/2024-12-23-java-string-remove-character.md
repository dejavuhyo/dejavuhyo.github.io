---
title: Java String에서 문자 제거
author: dejavuhyo
date: 2024-12-23 14:15:00 +0900
categories: [Language, Java]
tags: [java-string, string-remove-character, remove-character, 자바-스트링, 스트링-문자-제거, 문자-제거]
---

## 1. 문자열에서 문자 제거
Java String 클래스에는 다양한 `replace()` 메서드가 있다. 이를 사용하여 문자열에서 문자를 제거할 수 있다.

### 1) replace(char oldChar, char newChar)
이 메서드는 oldChar를 newChar로 대체한 새 문자열을 반환한다. 이 메서드는 oldChar의 모든 발생을 newChar 문자로 대체한다.

문자열에서 모든 문자를 제거하는 간단한 예이다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String s1 = "Hello";
        System.out.println(s1);
        s1 = s1.replace("l", "");
        System.out.println(s1);
    }
}
```

* 결과

```text
Hello
Heo
```

### 2) replace(CharSequence target, CharSequence replacement)
이 방법은 대상 하위 문자열을 대체 하위 문자열로 대체한다. 이 방법은 대상 하위 문자열의 모든 일치 항목을 대체 하위 문자열로 대체한다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String s1 = "Java Python Spring Python";
        System.out.println(s1);
        String s2 = s1.replace("Python", "");
        System.out.println(s2);
    }
}
```

* 결과

```text
Java Python Spring Python
Java  Spring 
```

### 3) replaceFirst(String regex, String replacement)
이 방법은 정규식의 첫 번째 일치 항목을 대체 문자열로 대체한다. 이 방법은 하위 문자열의 첫 번째 발생만 대체해야 할 때 유용하다.

`replaceFirst()` 메서드와 `replaceAll()` 메서드는 첫 번째 인수로 정규 표현식을 허용한다. 이를 사용하여 문자열에서 패턴을 제거할 수 있다. 예를 들어 문자열에서 소문자를 모두 제거한다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String s1 = "Hi Hello";
        System.out.println(s1);
        String s2 = s1.replaceAll("([a-z])", "");
        System.out.println(s2);
        String s3 = s1.replaceFirst("([a-z])", "");
        System.out.println(s3);
    }
}
```

* 결과

```text
Hi Hello
H H
H Hello
```


### 4) replaceAll(String regex, String replacement)
`replaceFirst()` 메서드와 같다. 유일한 차이점은 일치하는 정규 표현식의 모든 발생이 대체 문자열로 대체된다는 것이다.

문자열에서 모든 공백을 제거하는 예제이다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String s1 = "Hello World 2019";
        System.out.println(s1);
        String s2 = s1.replace(" ", "");
        System.out.println(s2);
    }
}
```

* 결과

```text
Hello World 2019
HelloWorld2019
```

문자열에서 탭 문자와 공백을 제거하는 방법이다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String s1 = "Hello World\t2019";
        System.out.println(s1);
        String s2 = s1.replace(" ", "");
        System.out.println(s2);
        String s3 = s2.replace("\t", "");
        System.out.println(s3);
    }
}
```

* 결과

```text
Hello World	2019
HelloWorld	2019
HelloWorld2019
```

정규식을 사용할 수도 있다.

```java
public class Test {
    public static void main(String[] args) {
        String s1 = "Hello World\t2019";
        System.out.println(s1);
        String s2 = s1.replaceAll("\\s", "");
        System.out.println(s2);
    }
}
```

* 결과

```text
Hello World	2019
HelloWorld2019
```

> 빈 문자 상수는 없다. 따라서 첫 번째 `replace(char c1, char c2)` 방법을 사용하여 문자열에서 문자를 제거할 수 없다. 빈 문자열을 대체 문자열로 전달하여 다른 세 가지 방법 중 하나를 사용해야 한다.

문자열에서 문자를 제거하려면 `remove()` 메서드가 필요하지 않다. `replace()` 메서드는 이 작업에 충분하다.

## [출처 및 참고]
* [https://www.javastring.net/java/string/remove-character-from-string-in-java](https://www.javastring.net/java/string/remove-character-from-string-in-java)
