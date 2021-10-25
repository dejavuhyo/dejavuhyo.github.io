---
title: 정수 오버플로우
author: dejavuhyo
date: 2021-10-13 06:00:00 +0900
categories: [Language, Java]
tags: [integer-overflow, wraparound, 정수-오버플로우]
---

## 1. 정의
정수형 변수의 오버플로우는 정숫값이 증가하면서 Java에서 허용된 가장 큰 값보다 더 커져서 실제 저장되는 값은 의도하지 않게 아주 작은 수이거나 음수가 될 수 있다. 특히 반복문 제어, 메모리 할당, 메모리 복사 등을 위한 조건으로 사용자가 제공하는 입력값을 사용하고 그 과정에서 정수 오버플로우가 발생하는 경우 보안상 문제를 유발할 수 있다.

## 2. 안전한 코딩 기법

* 언어/플랫폼별 정수 타입의 범위를 확인하여 사용한다. 정수형 변수를 연산에 사용하는 경우 결괏값이 범위 체크하는 모듈을 사용한다. 특히 회부 입력값을 동적으로 할당하여 사용하는 경우 변수의 값 범위를 검사하여 적절한 범위 내에 존재하는 값인지 확인한다.

## 3. 예제
다음의 예제는 외부의 입력(args[0], args[1])을 이용하여 동적으로 계산한 값을 배열의 크기(size)를 결정하는 데 사용하고 있다. 만일 외부 입력으로부터 계산된 값(size)이 오버플로우에 의해 음수값이 되면 배열의 크기가 음수가 되어 코드에 문체가 발생할 수 있다.

* 안전하지 않은 코드의 예

```java
public static void main(String[] args) {
    int size = new Integer(args[0]).intValue();
    size += new Integer(args[1]).intValue();
    MyClass[] data = new MyClass[size];
}
```

동적 메모리 할당을 위해 크기를 사용하는 경우 그 값이 음수가 아닌지 검사하는 문장이 필요하다.

* 안전한 코드의 예

```java
public static void main(String[] args) {
    int size = new Integer(args[0]).intValue();
    size += new Integer(args[1]).intValue();
    // 배열의 크기 값이 음수값이 아닌지 검사한다.
    if (size < 0) return ;
    MyClass[ ] data = new MyClass[size];
}
```

## [출처 및 참고]
* <https://www.kisa.or.kr/public/laws/laws3_View.jsp?cPage=6&mode=view&p_No=259&b_No=259&d_No=55&ST=T&SV=>
