---
title: Java 비트 연산자
author: dejavuhyo
date: 2022-09-05 09:20:00 +0900
categories: [Language, Java]
tags: [java-bitwise-operator, bitwise-operator, operator, 자바-비트-연산자, 비트-연산자, 연산자]
---

## 1. 비트 연산자(bitwise operator)
비트 연산자는 논리 연산자와 비슷하지만, 비트(bit) 단위로 논리 연산을 할 때 사용하는 연산자이다.

또한, 비트 단위로 왼쪽이나 오른쪽으로 전체 비트를 이동하거나, 1의 보수를 만들 때도 사용된다.

| 비트 연산자 | 설명 |
|:-----:|:-----:|
| `&` | 대응되는 비트가 모두 1이면 1을 반환함 (비트 AND 연산) |
| `|` | 대응되는 비트 중에서 하나라도 1이면 1을 반환함 (비트 OR 연산) |
| `^` | 대응되는 비트가 서로 다르면 1을 반환함 (비트 XOR 연산) |
| `~` | 비트를 1이면 0으로, 0이면 1로 반전시킴 (비트 NOT 연산, 1의 보수) |
| `<<` | 명시된 수만큼 비트들을 전부 왼쪽으로 이동시킴 (left shift 연산) |
| `>>` | 부호를 유지하면서 지정한 수만큼 비트를 전부 오른쪽으로 이동시킴 (right shift 연산) |
| `>>>` | 지정한 수만큼 비트를 전부 오른쪽으로 이동시키며, 새로운 비트는 전부 0이 됨 |

다음 그림은 비트 AND 연산자(&)의 동작을 나타낸다.

이처럼 비트 AND 연산자는 대응되는 두 비트가 모두 1일 때만 1을 반환하며, 다른 경우는 모두 0을 반환한다.

![bitwise-and](/assets/img/2022-09-05-java-bitwise-operator/bitwise-and.png)

다음 그림은 비트 OR 연산자(`|`)의 동작을 나타낸다.

이처럼 비트 OR 연산자는 대응되는 두 비트 중 하나라도 1이면 1을 반환하며, 두 비트가 모두 0일 때만 0을 반환한다.

![bitwise-or](/assets/img/2022-09-05-java-bitwise-operator/bitwise-or.png)

다음 그림은 비트 XOR 연산자(`^`)의 동작을 나타낸다.

이처럼 비트 XOR 연산자는 대응되는 두 비트가 서로 다르면 1을 반환하고, 서로 같으면 0을 반환한다.

![bitwise-xor](/assets/img/2022-09-05-java-bitwise-operator/bitwise-xor.png)

다음 그림은 비트 NOT 연산자(~)의 동작을 나타낸다.

이처럼 비트 NOT 연산자는 해당 비트가 1이면 0을 반환하고, 0이면 1을 반환한다.

![bitwise-not](/assets/img/2022-09-05-java-bitwise-operator/bitwise-not.png)

* 예제

```java
int num1 = 8, num2 = -8;

① System.out.println("~ 연산자에 의한 결과 : "+ ~num1);
② System.out.println("<< 연산자에 의한 결과 : "+ (num1 << 2));
③ System.out.println(">> 연산자에 의한 결과 : "+ (num2 >> 2));
④ System.out.println(">>> 연산자에 의한 결과 : "+ (num1 >>> 2));
⑤ System.out.println(">>> 연산자에 의한 결과 : "+ (num2 >>> 2));
```

# 실행 결과

```text
~ 연산자에 의한 결과 : -9
<< 연산자에 의한 결과 : 32
>> 연산자에 의한 결과 : -2
>>> 연산자에 의한 결과 : 2
>>> 연산자에 의한 결과 : 1073741822
```

위 예제의 ①번 라인에서 비트 반전 연산자(`~`)는 피연산자의 1의 보수를 반환하므로, 피연산자의 부호만 반대로 변경된다.

②번 라인의 왼쪽 시프트 연산자(`<<`)는 지정한 수만큼 피연산자의 모든 비트를 전부 왼쪽으로 이동시킨다.

이때 비트의 이동으로 새로 생기는 오른쪽 비트들은 언제나 0으로 채워진다.

실행 결과를 살펴보면, 모든 비트가 한 비트씩 왼쪽으로 이동할 때마다 그 값은 2배씩 증가한다는 사실을 알 수 있다.

![left-shift](/assets/img/2022-09-05-java-bitwise-operator/left-shift.png)

또한, ③번 라인의 오른쪽 시프트 연산자(`>>`)는 지정한 수만큼 피연산자의 모든 비트를 전부 오른쪽으로 이동시킨다.

이때 비트의 이동으로 새로 생기는 왼쪽 비트들은 양수일 경우에는 모두 0으로 채워지며, 음수일 경우에는 모두 1로 채워진다.

따라서 부호는 변하지 않는다.

실행 결과를 살펴보면, 모든 비트가 한 비트씩 오른쪽으로 이동할 때마다 그 값은 2배씩 감소한다는 사실을 알 수 있다.

![right-shift](/assets/img/2022-09-05-java-bitwise-operator/right-shift.png)

④번 라인의 오른쪽 시프트 연산자(`>>>`)는 부호 비트까지 포함하여 모든 비트를 전부 오른쪽으로 이동시킨다.

이때 비트의 이동으로 새로 생기는 왼쪽 비트들은 언제나 0으로 채워진다.

따라서 피연산자가 양수인 경우에는 부호 비트를 이동하지 않는 오른쪽 시프트 연산자(`>>`)와 같은 결과를 반한다.

![right-shift-positive](/assets/img/2022-09-05-java-bitwise-operator/right-shift-positive.png)

하지만 피연산자가 음수인 경우에는 부호 비트까지도 이동하므로, 전혀 다른 결과가 반환된다.

다음 그림은 1바이트의 경우일 때 연산 결과를 나타나며, 위의 예제에서는 총 4바이트일 경우의 연산 결과를 보여준다.

![right-shift-negative](/assets/img/2022-09-05-java-bitwise-operator/right-shift-negative.png)

따라서 이 시프트 연산자는 10진수의 연산보다는 2진수의 연산에서만 주로 사용된다.

## [출처 및 참고]
* <http://www.tcpschool.com/java/java_operator_bitwise>
