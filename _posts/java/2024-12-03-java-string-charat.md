---
title: Java String charAt()
author: dejavuhyo
date: 2024-12-03 09:35:00 +0900
categories: [Language, Java]
tags: [java-charat, charat, string-charat]
---

## 1. charAt()
`charAt()` 메서드는 문자열에서 지정된 인덱스에 있는 문자를 반환한다.

지정된 인덱스는 매개변수로 제공되며 범위는 `0`에서 `n-1`까지이다. 여기서 `n`은 문자열의 길이를 나타낸다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello, World!";
        System.out.println(str.charAt(0));
        System.out.println(str.charAt(2));
        System.out.println(str.charAt(4));
    }
}
```

* 결과

```text
H
l
o
```

## [출처 및 참고]
* [https://www.w3schools.com/java/ref_string_charat.asp](https://www.w3schools.com/java/ref_string_charat.asp)
* [https://codegym.cc/ko/groups/posts/ko.954.--charat](https://codegym.cc/ko/groups/posts/ko.954.--charat)
