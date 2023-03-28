---
title: Java Lambda Expressions
author: dejavuhyo
date: 2023-01-20 16:00:00 +0900
categories: [Language, Java]
tags: [java-lambda-expressions, lambda-expressions, java-lambda, lambda, 람다-식, 자바-람다, 자바-람다-식, 람다]
---

## 1. Syntax
람다 식은 함수형 프로그래밍을 용이하게 하고 개발을 크게 단순화한다. 람다 식은 다음 구문으로 특징지어진다.

```java
parameter -> expression body
```

람다 식의 중요한 특성이다.

* 선택적 유형 선언: 매개변수의 유형을 선언할 필요가 없다. 컴파일러는 매개변수 값에서 동일한 것을 추론할 수 있다.

* 매개변수 주위의 선택적 괄호: 괄호 안에 단일 매개변수를 선언할 필요가 없다. 매개변수가 여러 개인 경우 괄호가 필요하다.

* 선택적 중괄호: 본문에 단일 문이 포함된 경우 식 본문에 중괄호를 사용할 필요가 없다.

* 선택적 반환 키워드: 본문에 값을 반환하는 단일 표현식이 있는 경우 컴파일러는 자동으로 값을 반환한다. 표현식이 값을 반환함을 나타내려면 중괄호가 필요하다.

## 2. Lambda Expressions Example

* JavaTest.java

```java
public class JavaTest {
    public static void main(String args[]) {
        JavaTest javaTest = new JavaTest();

        //with type declaration
        MathOperation addition = (int a, int b) -> a + b;

        //with out type declaration
        MathOperation subtraction = (a, b) -> a - b;

        //with return statement along with curly braces
        MathOperation multiplication = (int a, int b) -> {
            return a * b;
        };

        //without return statement and without curly braces
        MathOperation division = (int a, int b) -> a / b;

        System.out.println("10 + 5 = " + javaTest.operate(10, 5, addition));
        System.out.println("10 - 5 = " + javaTest.operate(10, 5, subtraction));
        System.out.println("10 x 5 = " + javaTest.operate(10, 5, multiplication));
        System.out.println("10 / 5 = " + javaTest.operate(10, 5, division));

        //without parenthesis
        GreetingService greetService1 = message ->
                System.out.println("Hello " + message);

        //with parenthesis
        GreetingService greetService2 = (message) ->
                System.out.println("Hello " + message);

        greetService1.sayMessage("Mahesh");
        greetService2.sayMessage("Suresh");
    }

    interface MathOperation {
        int operation(int a, int b);
    }

    interface GreetingService {
        void sayMessage(String message);
    }

    private int operate(int a, int b, MathOperation mathOperation) {
        return mathOperation.operation(a, b);
    }
}
```

* 결과

```text
10 + 5 = 15
10 - 5 = 5
10 x 5 = 50
10 / 5 = 2
Hello Mahesh
Hello Suresh
```

람다 식은 주로 기능 인터페이스의 인라인 구현, 즉 단일 메서드만 있는 인터페이스를 정의하는데 사용된다.

위의 예제에서는 MathOperation 인터페이스의 연산 방법을 정의하기 위해 다양한 유형의 람다 식을 사용했다. 그런 다음 GreetingService의 sayMessage 구현을 정의했다.

람다 식은 익명 클래스의 필요성을 없애고 Java에 매우 간단하면서도 강력한 기능적 프로그래밍 기능을 제공한다.

## 3. Scope
람다 식을 사용하면 모든 최종 변수 또는 사실상 최종 변수(한 번만 할당됨)를 참조할 수 있다. 변수에 값이 두 번째로 할당되면 람다 식에서 컴파일 오류가 발생한다.

* JavaTest.java

```java
public class JavaTest {
    final static String salutation = "Hello! ";

    public static void main(String args[]) {
        GreetingService greetingService = message ->
                System.out.println(salutation + message);
        greetingService.sayMessage("Mahesh");
    }

    interface GreetingService {
        void sayMessage(String message);
    }
}
```

* 결과

```text
Hello! Mahesh
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/java8/java8_lambda_expressions.htm](https://www.tutorialspoint.com/java8/java8_lambda_expressions.htm)
