---
title: ArrayList에서 toArray 사용시 주의점
author: dejavuhyo
date: 2016-07-01 16:00:00 +0900
categories: [Language, Java]
tags: [arraylist-toarray, arraylist, toarray]
---

## 1. 설명
아래 소스는 문제가 없어 보이고, 컴파일에서도 에러가 발생하지 않는다. 그러나 실행 시켜보면, java.lang.ClassCastException이 발생한다.

```java
ArrayList list = new ArrayList();
String a = null;
list.add(a);
String[] b = (String[])list.toArray();
```

## 2. 이유
toArray 에 파라미터를 넘겨주지 않으면 아래 회색 부분에서 Object 타입으로 객체를 생성한다.

### 1) ArrayList 내용

```java
public Object[] toArray() {
    return Arrays.copyOf(elementData, size);
}

public <T> T[] toArray(T[] a) {
    if (a.length < size)
    // Make a new array of a's runtime type, but my contents:
    return (T[]) Arrays.copyOf(elementData, size, a.getClass());

    System.arraycopy(elementData, 0, a, 0, size);

    if (a.length > size)
        a[size] = null;

    return a;
}
```

### 2) Arrays 내용

```java
public static <T> T[] copyOf(T[] original, int newLength) {
    return (T[]) copyOf(original, newLength, original.getClass());
}

public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
    T[] copy = ((Object)newType == (Object)Object[].class)
    ? (T[]) new Object[newLength]
    : (T[]) Array.newInstance(newType.getComponentType(), newLength);
    System.arraycopy(original, 0, copy, 0,
    Math.min(original.length, newLength));
    
    return copy;
}
```

## 3. 해결 방법

```java
ArrayList list = new ArrayList();
String a = null;
list.add(a);
String[] b = (String[])list.toArray(new String[0]);
```

## 4. 참고

### 1) a와 b는 같은 객체

```java
String[] a = new newString[1]
String[] b = (String[])list.toArray(a);
```

### 2) a와 b는 다른 객체

```java
String[] a = new newString[0]
String[] b = (String[])list.toArray(a);
```

## [출처 및 참고]
* [gnujava.com/board/article_view.jsp?article_no=5261&board_no=1&table_cd=EPAR01&table_no=01](gnujava.com/board/article_view.jsp?article_no=5261&board_no=1&table_cd=EPAR01&table_no=01)
