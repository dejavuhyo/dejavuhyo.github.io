---
title: Java String hashCode()
author: dejavuhyo
date: 2024-12-17 10:10:00 +0900
categories: [Language, Java]
tags: [java-string, string-hashcode, hashcode, 자바-스트링, 스트링-해시코드, 해시코드]
---

## 1. 문자열 해시코드 계산
문자열 해시 코드 계산은 아래의 논리를 따른다.

```java
int hashcode = s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1];
```

* `s[i]`는 i번째 인덱스의 문자이다.

* `^` 지수화를 나타낸다.

* n은 문자열의 길이이다.

## 2. hashCode() 예제
String hashCode() 메서드의 예제이다.

* 예제

```java
public class StringHashCode {
    public static void main(String[] args) {
        String s1 = "Java";
        String s2 = "Java";
        String s3 = new String("Java");

        System.out.println(s1.hashCode());
        System.out.println(s2.hashCode());
        System.out.println(s3.hashCode());
        System.out.println("".hashCode());
    }
}
```

* 결과

```text
2301506
2301506
2301506
0
```

## 3. hashCode() 및 equals()

* 두 문자열에 대해 `equals()`가 참이면, 그들의 `hashCode()`는 동일할 것이다.

* 두 문자열 `hashCode()`가 같다는 것은 두 문자열이 같다는 의미가 아니다.

문자열 문자가 해시코드를 계산하는데 사용되므로 첫 번째 문장은 항상 참이 된다. 위의 자바 예제 이를 확인할 수 있다.

두 번째 문장을 확인하기 위한 예제이다.

* 예제

```java
public class StringHashCode {
    public static void main(String[] args) {
        String a = "Siblings";
        String b = "Teheran";

        System.out.println(a.hashCode());
        System.out.println(b.hashCode());
        System.out.println(a.hashCode() == b.hashCode());
        System.out.println(a.equals(b));
    }
}
```

* 결과

```text
231609873
231609873
true
false
```

## 4. 해시코드 충돌
두 문자열이 동일한 해시코드를 가질 때 이를 해시코드 충돌이라고 한다. 해시코드 충돌이 발생하는 경우가 많다.

예를 들어 "Aa"와 "BB"는 해시코드 값이 2112이다.

두 문자열이 동일한지 확인하기 위해 `hashCode()` 메서드에 의존해서는 안된다. 문자열 클래스는 Object 클래스에서 이 함수를 재정의 한다. 문자열이 `get()` 및 `put()` 작업의 맵 키로 사용될 때 Java 내부에서 사용된다.

## [출처 및 참고]
* [https://www.javastring.net/java/string/java-string-hashcode](https://www.javastring.net/java/string/java-string-hashcode)
