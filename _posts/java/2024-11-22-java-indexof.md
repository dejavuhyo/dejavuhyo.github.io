---
title: Java indexOf()
author: dejavuhyo
date: 2024-11-22 13:43:00 +0900
categories: [Language, Java]
tags: [java-indexof, indexof, 자바-indexof, 자바-문자-위치]
---

## 1. Java 문자열 indexOf()
Java의 `indexOf()` 메소드는 주어진 문자열 내에서 문자 또는 하위 문자열이 처음 나타나는 위치를 반환한다. 문자나 부분 문자열을 찾을 수 없으면 메서드는 `-1`을 반환한다.

```java
public int indexOf(int ch)
public int indexOf(int ch, int fromIndex)
public int indexOf(String str)
public int indexOf(String str, int fromIndex)
```

## 2. 문자열에서 문자 위치 찾기
문자열에서 문자 'o'의 위치를 ​​찾으려면 다음 코드를 사용한다.

### 1) indexOf(int ch)

* 예제 

```java
public static void main(String[] args) {
    String str = "Hello, world!";

    int position = str.indexOf('o');
    System.out.println("Position of 'o' in the string is: " + position);
}
```

* 결과

```text
Position of 'o' in the string is: 4
```

### 2) indexOf(int ch, int fromIndex)

* 예제

```java
public static void main(String[] args) {
    String str = "Hello, world!";
    
    int position = str.indexOf('o');

    while (position >= 0) {
        System.out.println("'o' found at position " + position);
        position = str.indexOf('o', position + 1);
    }
}
```

* 결과

```text
'o' found at position 4
'o' found at position 8
```

### 3) indexOf(String str)

* 예제

```java
public static void main(String[] args) {
    String str = "Java is a popular programming language";
    
    int position = str.indexOf("programming");
    System.out.println("Position of 'programming' in the string is: " + position);
}
```

* 결과

```text
Position of 'programming' in the string is: 18
```

### 4) indexOf(String str, int fromIndex)

* 예제

```java
public static void main(String[] args) {
    String str = "Java is a popular programming language. Java is widely used in web development.";

    int position = -1;


    while ((position = str.indexOf('a', position + 1)) != -1) {
        System.out.println("Found 'a' at position: " + position);
    }

    while ((position = str.indexOf("Java", position + 1)) != -1) {
        System.out.println("Found 'Java' at position: " + position);
    }
}
```

* 결과

```text
Found 'a' at position: 1
Found 'a' at position: 3
Found 'a' at position: 8
Found 'a' at position: 15
Found 'a' at position: 23
Found 'a' at position: 31
Found 'a' at position: 35
Found 'a' at position: 41
Found 'a' at position: 43
Found 'Java' at position: 0
Found 'Java' at position: 40
```

## [출처 및 참고]
* [https://codegym.cc/ko/groups/posts/ko.1064.--indexof](https://codegym.cc/ko/groups/posts/ko.1064.--indexof)
