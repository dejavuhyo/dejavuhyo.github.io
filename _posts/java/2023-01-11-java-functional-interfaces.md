---
title: Java Functional Interfaces
author: dejavuhyo
date: 2023-01-11 21:30:00 +0900
categories: [Language, Java]
tags: [java-functional-interfaces, functional-interfaces, 자바-기능적-인터페이스, 기능적-인터페이스, 기능-인터페이스]
---

## 1. Functional 인터페이스
Functional 인터페이스에는 표시할 단일 기능이 있다. 예를 들어, 단일 메서드 `compareTo`가 있는 `Comparable` 인터페이스는 비교 목적으로 사용된다.

Java 8은 람다 식에서 광범위하게 사용되는 많은 Functional 인터페이스를 정의했다. 다음은 `java.util.Function` 패키지에 정의된 기능 인터페이스 목록이다.

| Interface | Description |
|:-----:|:-----:|
| `BiConsumer<T,U>` | 두 개의 입력 인수를 받아들이고 결과를 반환하지 않는 작업을 나타낸다. |
| `BiFunction<T,U,R>` | 두 개의 인수를 받아들이고 결과를 생성하는 함수를 나타낸다. |
| `BinaryOperator<T>` | 동일한 형식의 두 피연산자에 대한 연산을 나타내며 피연산자와 동일한 형식의 결과를 생성한다. |
| `BiPredicate<T,U>` | 두 인수의 술어(부울 값 함수)를 나타낸다. |
| `BooleanSupplier` | 부울 값 결과의 공급자를 나타낸다. |
| `Consumer<T>` | 단일 입력 인수를 수락하고 결과를 반환하지 않는 작업을 나타낸다. |
| `DoubleBinaryOperator` | 두 개의 이중 값 피연산자에 대한 작업을 나타내고 이중 값 결과를 생성한다. |
| `DoubleConsumer` | 단일 이중 값 인수를 허용하고 결과를 반환하지 않는 작업을 나타낸다. |
| `DoubleFunction<R>` | 이중 값 인수를 허용하고 결과를 생성하는 함수를 나타낸다. |
| `DoublePredicate` | 하나의 이중 값 인수의 조건자(부울 값 함수)를 나타낸다. |
| `DoubleSupplier` | 이중 값 결과의 공급자를 나타낸다. |
| `DoubleToIntFunction` | 이중 값 인수를 허용하고 int 값 결과를 생성하는 함수를 나타낸다. |
| `DoubleToLongFunction` | 이중 값 인수를 허용하고 긴 값 결과를 생성하는 함수를 나타낸다. |
| `DoubleUnaryOperator` | 이중 값 결과를 생성하는 단일 이중 값 피연산자에 대한 연산을 나타낸다. |
| `Function<T,R>` | 하나의 인수를 받아들이고 결과를 생성하는 함수를 나타낸다. |
| `IntBinaryOperator` | 두 개의 int 값 피연산자에 대한 연산을 나타내고 int 값 결과를 생성한다. |
| `IntConsumer` | 단일 int 값 인수를 허용하고 결과를 반환하지 않는 작업을 나타낸다. |
| `IntFunction<R>` | int 값 인수를 받아들이고 결과를 생성하는 함수를 나타낸다. |
| `IntPredicate` | 하나의 int 값 인수의 조건자(부울 값 함수)를 나타낸다. |
| `IntSupplier` | int 값 결과의 공급자를 나타낸다. |
| `IntToDoubleFunction` | int 값 인수를 허용하고 이중 값 결과를 생성하는 함수를 나타낸다. |
| `IntToLongFunction` | int 값 인수를 허용하고 long 값 결과를 생성하는 함수를 나타낸다. |
| `IntUnaryOperator` | int 값 결과를 생성하는 단일 int 값 피연산자에 대한 작업을 나타낸다. |
| `LongBinaryOperator` | 두 개의 long 값 피연산자에 대한 연산을 나타내고 long 값 결과를 생성한다. |
| `LongConsumer` | 하나의 긴 값 인수를 받아들이고 결과를 반환하지 않는 작업을 나타낸다. |
| `LongFunction<R>` | 긴 값 인수를 받아들이고 결과를 생성하는 함수를 나타낸다. |
| `LongPredicate` | 긴 값 인수 하나의 조건자(부울 값 함수)를 나타낸다. |
| `LongSupplier` | 장기적인 가치를 제공하는 공급자를 나타낸다. |
| `LongToDoubleFunction` | long 값 인수를 허용하고 이중 값 결과를 생성하는 함수를 나타낸다. |
| `LongToIntFunction` | long 값 인수를 허용하고 int 값 결과를 생성하는 함수를 나타낸다. |
| `LongUnaryOperator` | long 값 결과를 생성하는 하나의 long 값 피연산자에 대한 연산을 나타낸다. |
| `ObjDoubleConsumer<T>` | 개체 값 인수와 이중 값 인수를 받아들이고 결과를 반환하지 않는 작업을 나타낸다. |
| `ObjIntConsumer<T>` | 개체 값 및 int 값 인수를 받아들이고 결과를 반환하지 않는 작업을 나타낸다. |
| `ObjLongConsumer<T>` | 개체 값 인수와 긴 값 인수를 받아들이고 결과를 반환하지 않는 작업을 나타낸다. |
| `Predicate<T>` | 한 인수의 조건자(부울 값 함수)를 나타낸다. |
| `Supplier<T>` | 결과 공급자를 나타낸다. |
| `ToDoubleBiFunction<T,U>` | 두 개의 인수를 받아들이고 값이 두 배인 결과를 생성하는 함수를 나타낸다. |
| `ToDoubleFunction<T>` | 이중 값 결과를 생성하는 함수를 나타낸다. |
| `ToIntBiFunction<T,U>` | 두 개의 인수를 받아들이고 int 값 결과를 생성하는 함수를 나타낸다. |
| `ToIntFunction<T>` | int 값 결과를 생성하는 함수를 나타낸다. |
| `ToLongBiFunction<T,U>` | 두 개의 인수를 받아들이고 긴 값의 결과를 생성하는 함수를 나타낸다. |
| `ToLongFunction<T>` | long 값 결과를 생성하는 함수를 나타낸다. |
| `UnaryOperator<T>` | 피연산자와 동일한 유형의 결과를 생성하는 단일 피연산자에 대한 연산을 나타낸다. |

## 2. Functional Interface Example
`Predicate <T>` 인터페이스는 부울 값을 반환하는 test(Object) 메서드가 있는 Functional 인터페이스이다. 이 인터페이스는 개체가 참 또는 거짓으로 테스트되었음을 ​​나타낸다.

* JavaTest.java

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;

public class JavaTest {
    public static void main(String args[]) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);

        // Predicate<Integer> predicate = n -> true
        // n is passed as parameter to test method of Predicate interface
        // test method will always return true no matter what value n has.

        System.out.println("Print all numbers:");

        //pass n as parameter
        eval(list, n -> true);

        // Predicate<Integer> predicate1 = n -> n%2 == 0
        // n is passed as parameter to test method of Predicate interface
        // test method will return true if n%2 comes to be zero

        System.out.println("Print even numbers:");
        eval(list, n -> n % 2 == 0);

        // Predicate<Integer> predicate2 = n -> n > 3
        // n is passed as parameter to test method of Predicate interface
        // test method will return true if n is greater than 3.

        System.out.println("Print numbers greater than 3:");
        eval(list, n -> n > 3);
    }

    public static void eval(List<Integer> list, Predicate<Integer> predicate) {

        for (Integer n : list) {

            if (predicate.test(n)) {
                System.out.println(n + " ");
            }
        }
    }
}
```

* 결과

```text
Print all numbers:
1
2
3
4
5
6
7
8
9
Print even numbers:
2
4
6
8
Print numbers greater than 3:
4
5
6
7
8
9
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/java8/java8_functional_interfaces.htm](https://www.tutorialspoint.com/java8/java8_functional_interfaces.htm)
