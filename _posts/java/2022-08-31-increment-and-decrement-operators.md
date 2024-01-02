---
title: Java 증감 연산자
author: dejavuhyo
date: 2022-08-31 09:00:00 +0900
categories: [Language, Java]
tags: [java-increment-decrement-operators, increment-decrement-operators, increment-operators, decrement-operators, operator, 자바-증감-연산자, 증감-연산자, 증가-연산자, 감소-연산자, 연산자]
---

## 1. 증감 연산자(increment and decrement operators)
증감 연산자는 피연산자를 1씩 증가 혹은 감소시킬 때 사용하는 연산자이다.

이 연산자는 피연산자가 단 하나뿐인 단항 연산자이다.

증감 연산자는 해당 연산자가 피연산자의 어느 쪽에 위치하는가에 따라 연산의 순서 및 결과가 달라진다.

| 증감 연산자 | 설명 |
|:-----:|:-----:|
| `++x` | 먼저 피연산자의 값을 1 증가시킨 후에 해당 연산을 진행함 |
| `x++` | 먼저 해당 연산을 수행하고 나서, 피연산자의 값을 1 증가시킴 |
| `--x` | 먼저 피연산자의 값을 1 감소시킨 후에 해당 연산을 진행함 |
| `x--` | 먼저 해당 연산을 수행하고 나서, 피연산자의 값을 1 감소시킴 |

## 2. 증감 연산자의 연산 순서
증감 연산자는 피연산자의 어느 쪽에 위치하는가에 따라 연산의 순서가 달라진다.

다음 예제는 증감 연산자의 연산 순서를 살펴보기 위한 예제이다.

* 예제

```java
int x = 10;
int y = x-- + 5 + --x;
System.out.println("x : "+ x + ", y : " + y);
```

* 실행 결과

```text
x : 8, y : 23
```

다음 그림은 위의 예제에서 수행되는 연산의 순서를 보여준다.

![decrement](/assets/img/2022-08-31-increment-and-decrement-operators/decrement.png)

* ①: 첫 번째 감소 연산자(decrement operator)는 피연산자의 뒤쪽에 위치하므로, 덧셈 연산이 먼저 수행된다.

* ②: 덧셈 연산이 수행된 후에 감소 연산이 수행된다. (x의 값 : 9)

* ③: 두 번째 감소 연산자는 피연산자의 앞쪽에 위치하므로, 덧셈 연산보다 먼저 수행된다. (x의 값 : 8)

* ④: 감소 연산이 수행된 후에 덧셈 연산이 수행된다.

* ⑤: 마지막으로 변수 y에 결괏값의 대입 연산이 수행된다. (y의 값 : 23)

## [출처 및 참고]
* [www.tcpschool.com/java/java_operator_incAndDec](www.tcpschool.com/java/java_operator_incAndDec)
