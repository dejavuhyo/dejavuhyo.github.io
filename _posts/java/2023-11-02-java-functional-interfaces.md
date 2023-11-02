---
title: Java 8 Functional 인터페이스
author: dejavuhyo
date: 2023-11-02 21:40:00 +0900
categories: [Language, Java]
tags: [java-interfaces, functional-interfaces, interfaces, 자바-인터페이스, 기능적-인터페이스, 인터페이스]
---

## 1. Java 8의 람다
Java 8은 람다 표현식의 형태로 강력하고 새로운 구문 개선을 가져왔다. 람다는 일류 언어 시민으로서 처리할 수 있는 익명 함수이다. 예를 들어, 이를 메서드에 전달하거나 메서드에서 반환할 수 있다.

Java 8 이전에는 일반적으로 단일 기능을 캡슐화해야 하는 모든 경우에 대해 클래스를 만들었다. 이는 기본 함수 표현 역할을 하는 것을 정의하기 위한 불필요한 상용구 코드가 많다는 것을 의미한다.

[Lambda Expressions and Functional Interfaces: Tips and Best Practices](https://www.baeldung.com/java-8-lambda-expressions-tips)에서는 람다 작업에 대한 기능적 인터페이스와 모범 사례를 더 자세히 설명한다. 이 글에서는 `java.util.function` 패키지에 있는 일부 특정 기능 인터페이스에 중점을 둔다.

## 2. Functional 인터페이스
모든 기능적 인터페이스에는 유익한 `@FunctionalInterface` 주석이 있는 것이 좋다. 이는 인터페이스의 목적을 명확하게 전달하며, 주석이 달린 인터페이스가 조건을 충족하지 않는 경우 컴파일러가 오류를 생성할 수도 있다.

SAM(Single Abstract Method)을 사용하는 모든 인터페이스는 기능적 인터페이스 이며 해당 구현은 람다 식으로 처리될 수 있다.

Java 8의 기본 메소드는 추상적이지 않으며 계산에 포함되지 않는다. 기능적 인터페이스에는 여전히 여러개의 기본 메소드가 있을 수 있다. 함수의 문서를 보면 이를 관찰할 수 있다.

## 3. Functions
람다의 가장 간단하고 일반적인 경우는 하나의 값을 받고 다른 값을 반환하는 메서드가 있는 기능적 인터페이스이다. 단일 인수로 구성된 이 함수는 인수 유형과 반환 값으로 매개변수화되는 Function 인터페이스로 표현된다.

```java
public interface Function<T, R> { … }
```

표준 라이브러리에서 Function 유형을 사용하는 방법 중 하나는 `Map.computeIfAbsent` 메서드이다. 이 메서드는 키별로 맵에서 값을 반환하지만 키가 맵에 아직 없는 경우 값을 계산한다. 값을 계산하기 위해 전달된 Function 구현을 사용한다.

```java
Map<String, Integer> nameMap = new HashMap<>();
Integer value = nameMap.computeIfAbsent("John", s -> s.length());
```

이 경우 키에 함수를 적용하고, 맵 안에 넣고, 메서드 호출에서 반환하여 값을 계산한다. 람다를 전달된 값 유형과 반환된 값 유형과 일치하는 메서드 참조로 바꿀 수 있다.

메소드를 호출하는 객체는 실제로 메소드의 암시적 첫 번째 인수다. 이를 통해 인스턴스 메서드 길이 참조를 Function 인터페이스로 캐스팅할 수 있다.

```java
Integer value = nameMap.computeIfAbsent("John", String::length);
```

Function 인터페이스에는 여러 함수를 하나로 결합하고 순차적으로 실행할 수 있는 기본 구성 메서드도 있다.

```java
Function<Integer, String> intToString = Object::toString;
Function<String, String> quote = s -> "'" + s + "'";

Function<Integer, String> quoteIntToString = quote.compose(intToString);

assertEquals("'5'", quoteIntToString.apply(5));
```

quoteIntToString 함수는 intToString 함수의 결과에 적용되는 quote 함수의 조합 이다.

## 4. 원시 함수 전문화
기본 유형은 일반 유형 인수가 될 수 없으므로 가장 많이 사용되는 기본 유형 double , int , long 및 인수 및 반환 유형의 조합에 대한 Function 인터페이스 버전이 있습니다.

* IntFunction, LongFunction, DoubleFunction: 인수는 지정된 유형이고 반환 유형은 매개변수화된다.

* ToIntFunction, ToLongFunction, ToDoubleFunction: 반환 유형은 지정된 유형이고 인수는 매개변수화된다.

* DoubleToIntFunction, DoubleToLongFunction, IntToDoubleFunction, IntToLongFunction, LongToIntFunction, LongToDoubleFunction: 이름에 지정된 대로 기본 유형으로 정의된 인수 및 반환 유형이 모두 있다.

예를 들어, short를 취하고 byte를 반환하는 함수에 대해 즉시 사용 가능한 기능적 인터페이스는 없지만 자체 작성을 방해하는 것은 없다.

```java
@FunctionalInterface
public interface ShortToByteFunction {

    byte applyAsByte(short s);

}
```

이제 ShortToByteFunction에 정의된 규칙을 사용하여 short 배열을 바이트 배열로 변환하는 메서드를 작성할 수 있다.

```java
public byte[] transformArray(short[] array, ShortToByteFunction function) {
    byte[] transformedArray = new byte[array.length];
    for (int i = 0; i < array.length; i++) {
        transformedArray[i] = function.applyAsByte(array[i]);
    }
    return transformedArray;
}
```

다음은 이를 사용하여 short 배열을 2를 곱한 바이트 배열로 변환하는 방법이다.

```java
short[] array = {(short) 1, (short) 2, (short) 3};
byte[] transformedArray = transformArray(array, s -> (byte) (s * 2));

byte[] expectedArray = {(byte) 2, (byte) 4, (byte) 6};
assertArrayEquals(expectedArray, transformedArray);
```

## 5. Two-Arity Function 전문화
두 개의 인수로 람다를 정의하려면 이름에 "Bi" 키워드가 포함된 추가 인터페이스(BiFunction, ToDoubleBiFunction, ToIntBiFunction 및 ToLongBiFunction)를 사용해야 한다.

BiFunction에는 생성된 인수와 반환 유형이 모두 있는 반면, ToDoubleBiFunction 및 기타 기능을 사용하면 기본 값을 반환할 수 있다.

표준 API에서 이 인터페이스를 사용하는 일반적인 예 중 하나는 `Map.replaceAll` 메서드이다. 이 메서드를 사용하면 맵의 모든 값을 일부 계산된 값으로 바꿀 수 있다.

급여에 대한 새 값을 계산하고 반환하기 위해 키와 이전 값을 받는 BiFunction 구현을 사용한다.

```java
Map<String, Integer> salaries = new HashMap<>();
salaries.put("John", 40000);
salaries.put("Freddy", 30000);
salaries.put("Samuel", 50000);

salaries.replaceAll((name, oldValue) -> name.equals("Freddy") ? oldValue : oldValue + 10000);
```

## 6. Suppliers
공급자 기능적 인터페이스는 인수를 취하지 않는 또 다른 기능 전문화이다. 우리는 일반적으로 값을 게으른 생성에 사용한다. 예를 들어 double 값을 제곱하는 함수를 정의한다. 값 자체는 받지 않지만 다음 값의 공급자는 다음과 같다.

```java
public double squareLazy(Supplier<Double> lazyValue) {
    return Math.pow(lazyValue.get(), 2);
}
```

이를 통해 공급자 구현을 사용하여 이 함수 호출에 대한 인수를 지연 생성할 수 있다. 이는 인수 생성에 상당한 시간이 걸리는 경우 유용할 수 있다. Guava의 sleepUninterruptible 메소드를 사용하여 이를 시뮬레이션한다.

```java
Supplier<Double> lazyValue = () -> {
    Uninterruptibles.sleepUninterruptibly(1000, TimeUnit.MILLISECONDS);
    return 9d;
};

Double valueSquared = squareLazy(lazyValue);
```

공급자의 또 다른 사용 사례는 시퀀스 생성을 위한 논리를 정의하는 것이다. 이를 시연하기 위해 정적 `Stream.generate` 메서드를 사용하여 피보나치 수열의 스트림을 생성한다.

```java
int[] fibs = {0, 1};
Stream<Integer> fibonacci = Stream.generate(() -> {
    int result = fibs[1];
    int fib3 = fibs[0] + fibs[1];
    fibs[0] = fibs[1];
    fibs[1] = fib3;
    return result;
});
```

`Stream.generate` 메소드에 전달하는 함수는 공급자 기능 인터페이스를 구현한다. 생성기로 유용하게 사용하려면 공급자는 일반적으로 일종의 외부 상태가 필요하다. 이 경우 해당 상태는 마지막 두 개의 피보나치 수열 번호로 구성된다.

이 상태를 구현하려면 람다 내부에 사용되는 모든 외부 변수가 사실상 final이어야 하므로 몇 가지 변수 대신 배열을 사용한다.

공급자 기능 인터페이스의 다른 전문화에는 BooleanSupplier, DoubleSupplier, LongSupplier 및 IntSupplier가 포함되며 반환 유형은 해당 기본 요소이다.

## 7. Consumers
공급자와 달리 소비자는 생성된 인수를 받아들이고 아무것도 반환하지 않는다. 부작용을 나타내는 함수이다.

예를 들어, 콘솔에 인사말을 인쇄하여 이름 목록에 있는 모든 사람에게 인사해본다. `List.forEach` 메서드에 전달된 람다는 소비자 기능 인터페이스를 구현한다.

```java
List<String> names = Arrays.asList("John", "Freddy", "Samuel");
names.forEach(name -> System.out.println("Hello, " + name));
```

기본 값을 인수로 받는 특수 버전의 소비자 (DoubleConsumer, IntConsumer 및 LongConsumer)도 있다. 더 흥미로운 것은 BiConsumer 인터페이스이다. 사용 사례 중 하나는 맵 항목을 반복하는 것이다.

```java
Map<String, Integer> ages = new HashMap<>();
ages.put("John", 25);
ages.put("Freddy", 24);
ages.put("Samuel", 30);

ages.forEach((name, age) -> System.out.println(name + " is " + age + " years old"));
```

특수화된 BiConsumer 버전의 또 다른 세트는 두 개의 인수를 받는 ObjDoubleConsumer, ObjIntConsumer 및 ObjLongConsumer로 구성된다. 인수 중 하나는 생성되고 다른 하나는 기본 유형이다.

## 8. Predicates
수학적 논리에서 조건자는 값을 받고 boolean 값을 반환하는 함수이다.

Predicate 함수 인터페이스는 생성된 값을 받고 boolean을 반환하는 함수의 특수화이다. 조건자 람다의 일반적인 사용 사례는 값 모음을 필터링하는 것이다.

```java
List<String> names = Arrays.asList("Angela", "Aaron", "Bob", "Claire", "David");

List<String> namesWithA = names.stream()
  .filter(name -> name.startsWith("A"))
  .collect(Collectors.toList());
```

위 코드에서는 Stream API를 사용하여 목록을 필터링하고 문자 "A"로 시작하는 이름만 유지한다. Predicate 구현은 필터링 논리를 캡슐화한다.

이전의 모든 예제와 마찬가지로 기본 값을 받는 이 함수의 IntPredicate, DoublePredicate 및 LongPredicate 버전이 있다.

## 9. Operators
연산자 인터페이스는 동일한 값 유형을 수신하고 반환하는 함수의 특수한 경우이다. UnaryOperator 인터페이스는 단일 인수를 받는다. Collections API의 사용 사례 중 하나는 목록의 모든 값을 동일한 유형의 일부 계산된 값으로 바꾸는 것이다.

```java
List<String> names = Arrays.asList("bob", "josh", "megan");

names.replaceAll(name -> name.toUpperCase());
```

`List.replaceAll` 함수는 현재 위치의 값을 대체하므로 void를 반환한다. 목적에 맞게 목록의 값을 변환하는데 사용되는 람다는 수신한 것과 동일한 결과 유형을 반환해야 한다. 이것이 UnaryOperator가 여기서 유용한 이유이다.

물론 `name -> name.toUpperCase()` 대신 간단히 메소드 참조를 사용할 수 있다.

```java
names.replaceAll(String::toUpperCase);
```

BinaryOperator의 가장 흥미로운 사용 사례 중 하나는 축소 작업이다. 모든 값의 합계로 정수 컬렉션을 집계한다고 가정해 본다. Stream API를 사용하면 수집기를 사용하여 이 작업을 수행할 수 있지만 이를 수행하는 보다 일반적인 방법은 축소 메서드를 사용하는 것이다.

```java
List<Integer> values = Arrays.asList(3, 5, 8, 9, 12);

int sum = values.stream()
  .reduce(0, (i1, i2) -> i1 + i2);
```

축소 메서드는 초기 누산기 값과 BinaryOperator 함수를 받는다. 이 함수의 인수는 동일한 유형의 값 쌍이다. 함수 자체에는 동일한 유형의 단일 값으로 결합하기 위한 논리도 포함되어 있다. 전달된 함수는 결합적이어야 한다. 즉, 값 집계 순서는 중요하지 않다. 즉, 다음 조건이 유지되어야 한다.

```java
op.apply(a, op.apply(b, c)) == op.apply(op.apply(a, b), c)
```

BinaryOperator 연산자 함수의 연관 속성을 사용 하면 축소 프로세스를 쉽게 병렬화할 수 있다.

물론 DoubleUnaryOperator, IntUnaryOperator, LongUnaryOperator, DoubleBinaryOperator, IntBinaryOperator 및 LongBinaryOperator와 같이 기본 값과 함께 사용할 수 있는 UnaryOperator 및 BinaryOperator의 전문화도 있다.

## 10. Legacy Functional Interfaces
모든 기능적 인터페이스가 Java 8에 등장한 것은 아니다. 이전 버전 Java의 많은 인터페이스는 FunctionalInterface의 제약 조건을 따르며 이를 람다로 사용할 수 있다. 대표적인 예로는 동시성 API에 사용되는 Runnable 및 Callable 인터페이스가 있다. Java 8에서는 이러한 인터페이스에도 `@FunctionalInterface` 주석이 표시된다. 이를 통해 동시성 코드를 크게 단순화할 수 있다.

```java
Thread thread = new Thread(() -> System.out.println("Hello From Another Thread"));
thread.start();
```

## [출처 및 참고]
* [https://www.baeldung.com/java-8-functional-interfaces](https://www.baeldung.com/java-8-functional-interfaces)
