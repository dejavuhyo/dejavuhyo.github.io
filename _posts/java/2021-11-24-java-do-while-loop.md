---
title: Java While Loop
author: dejavuhyo
date: 2021-11-24 09:30:00 +0900
categories: [Language, Java]
tags: [java-do-while-loop, do-while-loop, java-do-while, java-loop, 자바-do-while-loop, do-while-루프]
---

## 1. Do-While 루프
do-while 루프는 첫 번째 조건 평가가 루프의 첫 번째 반복 후에 이루어진다는 점을 제외하고는 while 루프와 동일하게 동작한다.

```java
do {
    statement;
} while (Boolean-expression);
```

간단한 예이다.

```java
int i = 0;
do {
    System.out.println("Do-While loop: i = " + i++);
} while (i < 5);
```

## [출처 및 참고]
* <https://www.baeldung.com/java-do-while-loop>
