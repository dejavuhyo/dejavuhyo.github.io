---
title: Java 무한대
author: dejavuhyo
date: 2022-10-24 09:30:00 +0900
categories: [Language, Java]
tags: [java-infinity, infinity, java-numbers, positive-infinity, negative-infinity, infinity-operations, 자바-무한대, 무한대, 자바-숫자, 양수-무한대, 음수-무한대, 무한대-연산]
---

## 1. 자바의 숫자
수학에는 실수 집합과 정수 집합이 있다. 분명히, 이 두 집합은 모두 무제한이며 양수 및 음수 무한대를 포함한다.

컴퓨터 세계에서는 이러한 집합에 대한 값을 저장할 수 있는 메모리 위치가 필요하며 컴퓨터의 메모리가 유한하므로 이 위치도 유한해야 한다.

Java의 int 유형의 경우 무한대 개념은 다루지 않는다. 선택한 메모리 위치에 맞는 정수만 저장할 수 있다.

실수의 경우 양수 또는 음수 무한대 개념도 있다. 32비트 float 유형과 64비트 double 유형은 Java에서 이를 지원한다. Java에서 실수에 가장 많이 사용되는 유형이기도 하므로 정밀도가 더 높기 때문이다.

## 2. 양의 무한대
`Double.POSITIVE_INFINITY` 상수는 double 유형의 양의 무한대 값을 보유한다. 이 값은 1.0을 0.0으로 나눈 값이다. String 표현은 `Infinity`이다. 이 값은 규칙이며 16진수 표현은 `7FF0000000000000`이다. 이 비트 단윗값이 있는 모든 이중 변수에는 양의 무한대가 포함된다.

## 3. 음의 무한대
`Double.NEGATIVE_INFINITY` 상수는 double 유형의 음의 무한대 값을 보유한다. 이 값은 -1.0을 0.0으로 나눈 값이다. String 표현은 `-Infinity`이다. 이 값도 규칙이며 16진수 표현은 `FFF0000000000000`이다. 이 비트 단윗값이 있는 모든 이중 변수에는 음의 무한대가 포함된다.

## 4. 무한대 연산
`positiveInfinity`라는 이중 변수를 선언하고 `Double.POSITIVE_INFINITY` 값과 또 다른 이중 변수 `negativeInfinity` 를 할당하고 `Double.NEGATIVE_INFINITY` 값을 할당한다. 그런 다음 작업에 대한 결과이다.

```java
Double positiveInfinity = Double.POSITIVE_INFINITY;
Double negativeInfinity = Double.NEGATIVE_INFINITY;

assertEquals(Double.NaN, (positiveInfinity + negativeInfinity));
assertEquals(Double.NaN, (positiveInfinity / negativeInfinity));
assertEquals(Double.POSITIVE_INFINITY, (positiveInfinity - negativeInfinity));
assertEquals(Double.NEGATIVE_INFINITY, (negativeInfinity - positiveInfinity));
assertEquals(Double.NEGATIVE_INFINITY, (positiveInfinity * negativeInfinity));
```

여기서 상수 `Double.NaN`은 숫자가 아닌 결과를 나타낸다. 무한대와 양수를 사용한 수학 연산이다.

```java
Double positiveInfinity = Double.POSITIVE_INFINITY;
Double negativeInfinity = Double.NEGATIVE_INFINITY;
double positiveNumber = 10.0; 

assertEquals(Double.POSITIVE_INFINITY, (positiveInfinity + positiveNumber));
assertEquals(Double.NEGATIVE_INFINITY, (negativeInfinity + positiveNumber));

assertEquals(Double.POSITIVE_INFINITY, (positiveInfinity - positiveNumber));
assertEquals(Double.NEGATIVE_INFINITY, (negativeInfinity - positiveNumber));

assertEquals(Double.POSITIVE_INFINITY, (positiveInfinity * positiveNumber));
assertEquals(Double.NEGATIVE_INFINITY, (negativeInfinity * positiveNumber));

assertEquals(Double.POSITIVE_INFINITY, (positiveInfinity / positiveNumber));
assertEquals(Double.NEGATIVE_INFINITY, (negativeInfinity / positiveNumber));

assertEquals(Double.NEGATIVE_INFINITY, (positiveNumber - positiveInfinity));
assertEquals(Double.POSITIVE_INFINITY, (positiveNumber - negativeInfinity));

assertEquals(0.0, (positiveNumber / positiveInfinity));
assertEquals(-0.0, (positiveNumber / negativeInfinity));
```

무한대와 음수를 사용한 수학 연산이다.

```java
Double positiveInfinity = Double.POSITIVE_INFINITY;
Double negativeInfinity = Double.NEGATIVE_INFINITY;
double negativeNumber = -10.0; 

assertEquals(Double.POSITIVE_INFINITY, (positiveInfinity + negativeNumber));
assertEquals(Double.NEGATIVE_INFINITY, (negativeInfinity + negativeNumber));

assertEquals(Double.POSITIVE_INFINITY, (positiveInfinity - negativeNumber));
assertEquals(Double.NEGATIVE_INFINITY, (negativeInfinity - negativeNumber));

assertEquals(Double.NEGATIVE_INFINITY, (positiveInfinity * negativeNumber));
assertEquals(Double.POSITIVE_INFINITY, (negativeInfinity * negativeNumber));

assertEquals(Double.NEGATIVE_INFINITY, (positiveInfinity / negativeNumber));
assertEquals(Double.POSITIVE_INFINITY, (negativeInfinity / negativeNumber));

assertEquals(Double.NEGATIVE_INFINITY, (negativeNumber - positiveInfinity));
assertEquals(Double.POSITIVE_INFINITY, (negativeNumber - negativeInfinity));

assertEquals(-0.0, (negativeNumber / positiveInfinity));
assertEquals(0.0, (negativeNumber / negativeInfinity));
```

이러한 작업을 더 잘 기억하기 위한 몇 가지 경험 법칙이 있다.

* 음의 무한대와 양의 무한대를 각각 `Infinity` 및 `-Infinity`로 바꾸고 부호 연산을 먼저 수행한다.

* 0과 무한대가 아닌 숫자 사이의 모든 연산에 대해 무한대의 결과를 얻게 된다.

* 양의 무한대와 음의 무한대를 더하거나 나눌 때 숫자 결과를 얻지 못한다.

## 5. 0으로 나누기
0으로 나누기는 음수 및 양수 무한대 값을 생성하기 때문에 나누기의 특별한 경우이다. 예를 들어, double값 d를 0으로 나눈 결과이다.

```java
double d = 1.0;

assertEquals(Double.POSITIVE_INFINITY, (d / 0.0));
assertEquals(Double.NEGATIVE_INFINITY, (d / -0.0));
assertEquals(Double.NEGATIVE_INFINITY, (-d / 0.0));
assertEquals(Double.POSITIVE_INFINITY, (-d / -0.0));
```

## [출처 및 참고]
* <https://www.baeldung.com/java-infinity>
