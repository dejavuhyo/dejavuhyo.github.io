---
title: Java 2차원 배열 값 출력
author: dejavuhyo
date: 2024-11-29 11:15:00 +0900
categories: [Language, Java]
tags: [java-array, array, array-value, 자바-배열, 배열, 배열-값]
---

2024-11-29-java-array-value.md

## 1. 반복문
이중 for문을 사용하여 값을 출력 한다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        int[][] ary = {{1, 2}, {3, 4, 5}, {6, 7, 8, 9}};
        for (int[] ar : ary) {
            for (int val : ar) {
                System.out.println(val);
            }
        }
    }
}
```

* 결과

```text
1
2
3
4
5
6
7
8
9
```

## 2. Arrays.deepToString()
`java.util.Arrays.deepToString()` 메소드를 사용하여 2차원 배열 값을 문자열로 출력 한다.

* 예제

```java
import java.util.Arrays;

public class Test {
    public static void main(String[] args) {
        int[][] ary = {{1, 2}, {3, 4, 5}, {6, 7, 8, 9}};
        System.out.println(Arrays.deepToString(ary));
    }
}
```

* 결과

```text
[[1, 2], [3, 4, 5], [6, 7, 8, 9]]
```

## [출처 및 참고]
* [https://hianna.tistory.com/511](https://hianna.tistory.com/511)
