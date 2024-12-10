---
title: Java List 오름차순, 내림차순 정렬
author: dejavuhyo
date: 2024-11-20 13:22:00 +0900
categories: [Language, Java]
tags: [java-list, list-asc, list-desc, list-sort, 자바-리스트, 리스트-오름차순, 리스트-내림차순, 리스트-정렬]
---

## 1. Collections.sort()

```java
public static void main(String[] args) {
    List<String> list = new ArrayList<>();
    list.add("b");
    list.add("a");
    list.add("C");
    list.add("d");
    list.add("B");
    list.add("c");
    list.add("A");

    Collections.sort(list);
    System.out.println("오름차순 = " + list); // [A, B, C, a, b, c, d]

    Collections.sort(list, Collections.reverseOrder());
    System.out.println("내림차순 = " + list); // [d, c, b, a, C, B, A]

    Collections.sort(list, String.CASE_INSENSITIVE_ORDER);
    System.out.println("대소문자 구분없이 오름차순 = " + list); //[a, A, b, B, c, C, d]

    Collections.sort(list, Collections.reverseOrder(String.CASE_INSENSITIVE_ORDER));
    System.out.println("대소문자 구분없이 내림차순 = " + list); // [d, c, C, b, B, a, A]
}
```


## 2. List.sort()
Java 8 이후부터는 `sort()` 메소드를 호출하여 정렬할 수 있다.

```java
public static void main(String[] args) {
    List<String> list = new ArrayList<>();
    list.add("b");
    list.add("a");
    list.add("C");
    list.add("d");
    list.add("B");
    list.add("c");
    list.add("A");

    list.sort(Comparator.naturalOrder());
    System.out.println("오름차순 = " + list);

    list.sort(Comparator.reverseOrder());
    System.out.println("내림차순 = " + list);

    list.sort(String.CASE_INSENSITIVE_ORDER);
    System.out.println("대소문자 구분없이 오름차순 = " + list);

    list.sort(Collections.reverseOrder(String.CASE_INSENSITIVE_ORDER));
    System.out.println("대소문자 구분없이 내림차순 = " + list);
}
```

## [출처 및 참고]
* [https://hianna.tistory.com/569](https://hianna.tistory.com/569)
