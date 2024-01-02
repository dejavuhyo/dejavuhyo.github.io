---
title: Java 논리 연산자
author: dejavuhyo
date: 2022-09-02 09:00:00 +0900
categories: [Language, Java]
tags: [java-logical-operator, logical-operator, operator, 자바-논리-연산자, 논리-연산자, 연산자]
---

## 1. 논리 연산자(logical operator)
논리 연산자는 주어진 논리식을 판단하여, 참(true)과 거짓(false)을 결정하는 연산자이다.

AND 연산과 OR 연산은 두 개의 피연산자를 가지는 이항 연산자이며, 피연산자들의 결합 방향은 왼쪽에서 오른쪽이다.

NOT 연산자는 피연산자가 단 하나뿐인 단항 연산자이며, 피연산자의 결합 방향은 오른쪽에서 왼쪽이다.

| 논리 연산자 | 설명 |
|:-----:|:-----:|
| `&&` | 논리식이 모두 참이면 참을 반환함 (논리 AND 연산) |
| `||` | 논리식 중에서 하나라도 참이면 참을 반환함 (논리 OR 연산) |
| `!` | 논리식의 결과가 참이면 거짓을, 거짓이면 참을 반환함 (논리 NOT 연산) |

다음은 논리 연산자의 모든 동작의 결과를 보여주는 진리표(truth table)이다.

| `A` | `B` | `A && B` | `A || B` | `!A` |
|:-----:|:-----:|:-----:|:-----:|:-----:|
| true | true | true | true | false |
| true | false | false | true | false |
| false | true | false | true | true |
| false | false | false | false | true |

* 예제

```java
char ch1 = 'b', ch2 = 'B';
boolean result1, result2;

result1 = (ch1 > 'a') && (ch1 < 'z') ;
result2 = (ch2 < 'A') || (ch2 < 'Z') ;

System.out.println("&& 연산자에 의한 결과 : "+ result1);
System.out.println("|| 연산자에 의한 결과 : "+ result2);
System.out.println("! 연산자에 의한 결과 : "+ !result2);
```

* 실행 결과

```text
&& 연산자에 의한 결과 : true
|| 연산자에 의한 결과 : true
! 연산자에 의한 결과 : false
```

위의 예제처럼 자바에서는 char형 문자끼리도 크기를 서로 비교할 수 있다.

## [출처 및 참고]
* [www.tcpschool.com/java/java_operator_logic](www.tcpschool.com/java/java_operator_logic)
