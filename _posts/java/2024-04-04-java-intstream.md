---
title: Java IntStream
author: dejavuhyo
date: 2024-04-04 11:47:00 +0900
categories: [Language, Java]
tags: [java-intstream, java-stream, intstream]
---

## 1. IntStream
IntStream은 기본(primitive) 정수 요소의 열을 만들거나 병렬 집계 처리를 지원한다.

## 2. 사용법

```java
import java.util.stream.IntStream;

public class IntStreamRange {

    public static void main(String[] args) {
        System.out.println("range:");
        IntStream.range(0, 10).forEach(System.out::println);

        System.out.println("rangeClosed:");
        IntStream.rangeClosed(0, 10).forEach(System.out::println);

        System.out.println("sum:");
        System.out.println(IntStream.range(0, 10).sum());

        System.out.println("max:");
        System.out.println(IntStream.range(0, 10).max());

        System.out.println("min:");
        System.out.println(IntStream.range(0, 10).min());

        System.out.println("average:");
        System.out.println(IntStream.range(0, 10).average());
    }
}
```

### 1) range()
0에서 9까지를 표시하는 예제이다. `range()`는 종료 값을 포함하지 않는다.

```java
IntStream.range(0, 10).forEach(System.out::println);
```

실행 결과

```text
0
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

### 2) rangeClose()
0에서 10까지를 표시하는 예제이다. `rangeClose()`는 종료 값을 포함한다.

```java
IntStream.rangeClosed(0, 10).forEach(System.out::println);
```

실행 결과

```text
0
1
2
3
4
5
6
7
8
9
10
```

###  3) sum()
`sum()`은 0에서 10까지의 합계를 다음과 같이 계산할 수 있다.

```java
System.out.println(IntStream.range(0, 10).sum());

실행 결과

```text
45
```

### 4) max()
`max()`는 쉽게 최대값을 구할 수 있다.

```java
System.out.println(IntStream.range(0, 10).max());
```

실행 결과

```text
OptionalInt[9]
```

### 5) min()
`min()`은 쉽게 최소값을 구할 수 있다.

```java
System.out.println(IntStream.range(0, 10).min());
```

실행 결과

```text
OptionalInt[0]
```

### 6) average()
`average()`는 평균을 구할 수 있다.

```java
System.out.println(IntStream.range(0, 10).average());
```

실행 결과

```text
OptionalDouble[4.5]
```

## [출처 및 참고]
* [https://www.devkuma.com/docs/java/stream-api/int-stream/](https://www.devkuma.com/docs/java/stream-api/int-stream/)
