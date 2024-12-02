---
title: Java String 문자열을 char 배열로 변환
author: dejavuhyo
date: 2024-12-02 13:50:00 +0900
categories: [Language, Java]
tags: [java-char, char, string-char, 자바-배열, 배열]
---

## 1. toCharArray()
`toCharArray()` 메소드는 문자열(String)을 char 배열로 변환한다. 반환되는 배열의 길이는 문자열의 길이와 같다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        String str = "Hello, World!";
        char[] ary = str.toCharArray();

        for (char c : ary) {
            System.out.println(c);
        }
    }
}
```

* 결과

```text
H
e
l
l
o
,
 
W
o
r
l
d
!
```

## [출처 및 참고]
* [https://bo5mi.tistory.com/49](https://bo5mi.tistory.com/49)
