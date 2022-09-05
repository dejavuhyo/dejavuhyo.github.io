---
title: Java 산술 연산자
author: dejavuhyo
date: 2022-08-29 09:20:00 +0900
categories: [Language, Java]
tags: [java-arithmetic-operator, arithmetic-operator, operator, 자바-산술-연산자, 산술-연산자, 연산자]
---

## 1. 산술 연산자(arithmetic operator)
산술 연산자는 사칙연산을 다루는 연산자로, 가장 기본적이면서도 가장 많이 사용되는 연산자 중 하나이다.

산술 연산자는 모두 두 개의 피연산자를 가지는 이항 연산자이며, 피연산자들의 결합 방향은 왼쪽에서 오른쪽이다.

> 항이란 해당 연산의 실행이 가능하기 위해 필요한 값이나 변수를 의미한다. 따라서 이항 연산자란 해당 연산의 실행을 위해서 두 개의 값이나 변수가 필요한 연산자를 의미한다.

| 산술 연산자 | 설명 |
|:-----:|:-----:|
| `+` | 왼쪽의 피연산자에 오른쪽의 피연산자를 더함 |
| `-` | 왼쪽의 피연산자에서 오른쪽의 피연산자를 뺌 |
| `*` | 왼쪽의 피연산자에 오른쪽의 피연산자를 곱함 |
| `/` | 왼쪽의 피연산자를 오른쪽의 피연산자로 나눔 |
| `%` | 왼쪽의 피연산자를 오른쪽의 피연산자로 나눈 후, 그 나머지를 반환함 |

* 예제

```java
int num1 = 8, num2 = 4;

System.out.println("+ 연산자에 의한 결과 : "+ (num1 + num2));
System.out.println("- 연산자에 의한 결과 : "+ (num1 - num2));
System.out.println("* 연산자에 의한 결과 : "+ (num1 * num2));
System.out.println("/ 연산자에 의한 결과 : "+ (num1 / num2));
System.out.println("% 연산자에 의한 결과 : "+ (num1 % num2));
```

* 실행 결과

```text
+ 연산자에 의한 결과 : 12
- 연산자에 의한 결과 : 4
* 연산자에 의한 결과 : 32
/ 연산자에 의한 결과 : 2
% 연산자에 의한 결과 : 0
```

## 2. 연산자의 우선순위(operator precedence)와 결합 방향(associativity)
연산자의 우선순위는 수식 내에 여러 연산자가 함께 등장할 때, 어느 연산자가 먼저 처리될 것인가를 결정한다.

다음 그림은 가장 높은 우선순위를 가지고 있는 괄호(`()`) 연산자를 사용하여 연산자의 처리 순서를 변경하는 것을 보여준다.

![precedence](/assets/img/2022-08-29-java-arithmetic-operator/precedence.png)

연산자의 결합 방향은 수식 내에 우선순위가 같은 연산자가 둘 이상 있을 때, 먼저 어느 연산을 수행할 것인가를 결정한다.

![associativity](/assets/img/2022-08-29-java-arithmetic-operator/associativity.png)

## 3. 자바 연산자의 우선순위표
자바에서 연산자의 우선순위와 결합 방향은 다음과 같다.

| 우선순위 | 연산자 | 설명 | 결합 방향 |
|:-----:|:-----:|:-----:|:-----:|
| 1 | `[]` | 첨자 연산자 | 왼쪽에서 오른쪽으로 |
|  | `.` | 멤버 연산자 | 왼쪽에서 오른쪽으로 |
| 2 | `++` | 후위 증가 연산자 | 왼쪽에서 오른쪽으로 |
|  | `--` | 후위 감소 연산자 | 왼쪽에서 오른쪽으로 |
| 3 | `!` | 논리 NOT 연산자 | 오른쪽에서 왼쪽으로 |
|  | `~` | 비트 NOT 연산자 | 오른쪽에서 왼쪽으로 |
|  | `+` | 양의 부호 (단항 연산자) | 오른쪽에서 왼쪽으로 |
|  | `-` | 음의 부호 (단항 연산자) | 오른쪽에서 왼쪽으로 |
|  | `++` | 전위 증가 연산자 | 오른쪽에서 왼쪽으로 |
|  | `--` | 전위 감소 연산자 | 오른쪽에서 왼쪽으로 |
|  | (타입) | 타입 캐스트 연산자 | 오른쪽에서 왼쪽으로 |
| 4 | `*` | 곱셈 연산자 | 왼쪽에서 오른쪽으로 |
|  | `/` | 나눗셈 연산자 | 왼쪽에서 오른쪽으로 |
|  | `%` | 나머지 연산자 | 왼쪽에서 오른쪽으로 |
| 5 | `+` | 덧셈 연산자 (이항 연산자) | 왼쪽에서 오른쪽으로 |
|  | `-` | 뺄셈 연산자 (이항 연산자) | 왼쪽에서 오른쪽으로 |
| 6 | `<<` | 비트 왼쪽 시프트 연산자 | 왼쪽에서 오른쪽으로 |
|  | `>>` | 부호 비트를 확장하면서 비트 오른쪽 시프트 | 왼쪽에서 오른쪽으로 |
|  | `>>>` | 부호 비트까지 모두 비트 오른쪽 시프트 | 왼쪽에서 오른쪽으로 |
| 7 | `<` | 관계 연산자(보다 작은) | 왼쪽에서 오른쪽으로 |
|  | `<=` | 관계 연산자(보다 작거나 같은) | 왼쪽에서 오른쪽으로 |
|  | `>` | 관계 연산자(보다 큰) | 왼쪽에서 오른쪽으로 |
|  | `>=` | 관계 연산자(보다 크거나 같은) | 왼쪽에서 오른쪽으로 |
|  | instanceof | 인스턴스의 실제 타입 반환 | 왼쪽에서 오른쪽으로 |
| 8 | `==` | 관계 연산자(와 같은) | 왼쪽에서 오른쪽으로 |
|  | `!=` | 관계 연산자(와 같지 않은) | 왼쪽에서 오른쪽으로 |
| 9 | `&` | 비트 AND 연산자 | 왼쪽에서 오른쪽으로 |
| 10 | `^` | 비트 XOR 연산자 | 왼쪽에서 오른쪽으로 |
| 11 | `|` | 비트 OR 연산자 | 왼쪽에서 오른쪽으로 |
| 12 | `&&` | 논리 AND 연산자 | 왼쪽에서 오른쪽으로 |
| 13 | `||` | 논리 OR 연산자 | 왼쪽에서 오른쪽으로 |
| 14 | `? :` | 삼항 조건 연산자 | 오른쪽에서 왼쪽으로 |
| 15 | `=` | 대입 연산자 및 복합 대입 연산자 (`=`, `+=`, `-=`, `*=`, `/=`, `%=`, `<<=`, `>>=`, `>>>=`, `&=`, `^=`, `|=`) | 오른쪽에서 왼쪽으로 |

위의 표에서 나온 순서대로 우선순위가 빠른 연산자가 가장 먼저 실행된다.

또한, 같은 우선순위를 가지는 연산자가 둘 이상 있을 때에는 결합 순서에 따라 실행 순서가 결정된다.

## [출처 및 참고]
* <http://www.tcpschool.com/java/java_operator_arithmetic>