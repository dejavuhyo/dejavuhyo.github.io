---
title: Java StringBuffer 메소드
author: dejavuhyo
date: 2024-11-25 10:30:00 +0900
categories: [Language, Java]
tags: [java-stringbuffer, stringbuffer-method, 자바-스트링버퍼, 스트링버퍼-메소드]
---

## 1. append()
문자열을 추가하기 위해 사용한다.

```java
public static void main(String[] args) {
    StringBuffer sb = new StringBuffer();

    sb.append("hello ");
    System.out.println(sb);

    sb.append("world!");
    System.out.println(sb);
}
```

## 2. length(), capacity()
StringBuffer의 문자열 길이나 전체 할당된 capacity를 구할 때 사용한다.

최초 설정된 10의 capacity를 넘는 문자열이 append 되면 자동으로 capacity가 22로 증가한다.

```java
public static void main(String[] args) {
    StringBuffer sb = new StringBuffer(10); // capacity 10으로 설정
    System.out.println("최초 문자열 길이 : " + sb.length() + ", 총 capacity : " + sb.capacity());
    
    sb.append("abcdefgh");
    System.out.println(sb);
    System.out.println("첫 번째 문자열 길이 : " + sb.length() + ", 총 capacity : " + sb.capacity());

    sb.append("ijklmn");
    System.out.println(sb);
    System.out.println("두 번째 문자열 길이 : " + sb.length() + ", 총 capacity : " + sb.capacity());
}
```

## 3. insert()
문자열 맨 뒤에 삽입하는 `append()`와 다르게 인덱스를 지정하여 원하는 위치에 삽입할 수 있다.

```java
public static void main(String[] args) {
    StringBuffer sb = new StringBuffer();

    sb.append("world");
    System.out.println(sb);

    sb.insert(0, "hello ");
    System.out.println(sb);
}
```

## 4. reverse()
문자열을 뒤집을 때 사용한다.

```java
public static void main(String[] args) {
    StringBuffer sb = new StringBuffer();

    sb.append("hello world");
    System.out.println(sb.reverse());
}
```

## 5. delete(), deleteCharAt()
인덱스를 지정하여 문자열의 일부분을 삭제하거나 한 문자만 삭제할 때 사용한다.

```java
public static void main(String[] args) {
    StringBuffer sb = new StringBuffer();

    sb.append("hello world");
    sb.delete(1, 3);
    System.out.println(sb);

    sb.deleteCharAt(2);
    System.out.println(sb);
}
```

## 6. replace()
문자열의 일부분을 다른 문자열로 치환할 때 사용한다.

```java
public static void main(String[] args) {
    StringBuffer sb = new StringBuffer();
    sb.append("hello world");

    sb.replace(6, 11, "friend");
    System.out.println(sb);
}
```

## [출처 및 참고]
* [https://yangbox.tistory.com/58](https://yangbox.tistory.com/58)
