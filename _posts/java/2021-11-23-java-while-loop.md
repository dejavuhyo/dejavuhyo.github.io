---
title: Java While Loop
author: dejavuhyo
date: 2021-11-23 09:30:00 +0900
categories: [Language, Java]
tags: [java-while-loop, while-loop, java-while, java-loop, 자바-while-loop, while-루프]
---

## 1. while 루프

while 루프는 자바의 가장 기본적인 루프문이다. 제어하는 부울 표현식이 true인 동안 명령문 또는 명령문 블록을 반복 한다.

while 루프의 구문은 다음과 같다.

```java
while (Boolean-expression) 
    statement;
```

루프의 부울 표현식은 루프의 첫 번째 반복 전에 평가 된다. 즉, 조건이 false로 평가되면 루프가 한 번도 실행되지 않을 수 있다.

간단한 예이다.

```java
int i = 0;
while (i < 5) {
    System.out.println("While loop: i = " + i++);
}
```

## [출처 및 참고]
* <https://www.baeldung.com/java-while-loop>
