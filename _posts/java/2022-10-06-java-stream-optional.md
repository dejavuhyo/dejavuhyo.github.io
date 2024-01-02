---
title: Java 스트림 Optional 클래스
author: dejavuhyo
date: 2022-10-06 09:40:00 +0900
categories: [Language, Java]
tags: [java-stream-optional, optional-class, java-stream, stream-optional, stream-optional-class, stream, 자바-스트림-optional, 스트림-optional, 스트림-optional-클래스, optional-클래스, 자바-스트림, 스트림]
---

## 1. java.util.Optional<T> 클래스
Optional<T> 클래스는 Integer나 Double 클래스처럼 'T'타입의 객체를 포장해 주는 래퍼 클래스(Wrapper class)이다. 따라서 Optional 인스턴스는 모든 타입의 참조 변수를 저장할 수 있다.

이러한 Optional 객체를 사용하면 예상치 못한 NullPointerException 예외를 제공되는 메소드로 간단히 회피할 수 있다.

즉, 복잡한 조건문 없이도 널(null) 값으로 인해 발생하는 예외를 처리할 수 있게 된다.

## 2. Optional 객체의 생성
`of()` 메소드나 `ofNullable()` 메소드를 사용하여 Optional 객체를 생성할 수 있습니다. `of()` 메소드는 null이 아닌 명시된 값을 가지는 Optional 객체를 반환한다.

만약 `of()` 메소드를 통해 생성된 Optional 객체에 null이 저장되면 NullPointerException 예외가 발생한다.

따라서 만약 참조 변수의 값이 만에 하나 null이 될 가능성이 있다면, `ofNullable()` 메소드를 사용하여 Optional 객체를 생성하는 것이 좋다.

`ofNullable()` 메소드는 명시된 값이 null이 아니면 명시된 값을 가지는 Optional 객체를 반환하며, 명시된 값이 null이면 비어있는 Optional 객체를 반환한다.

* 예제

```java
Optional<String> opt = Optional.ofNullable("자바 Optional 객체");
System.out.println(opt.get());
```

* 실행 결과

```text
자바 Optional 객체
```

## 3. Optional 객체에 접근
`get()` 메소드를 사용하면 Optional 객체에 저장된 값에 접근할 수 있다. 만약 Optional 객체에 저장된 값이 null이면, NoSuchElementException 예외가 발생한다.

따라서 `get()` 메소드를 호출하기 전에 `isPresent()` 메소드를 사용하여 Optional 객체에 저장된 값이 null인지 아닌지를 먼저 확인한 후 호출하는 것이 좋다.

다음 예제는 `isPresent()` 메소드를 이용하여 좀 더 안전하게 Optional 객체에 저장된 값에 접근하는 예제이다.

* 예제

```java
Optional<String> opt = Optional.ofNullable("자바 Optional 객체");

if(opt.isPresent()) {
    System.out.println(opt.get());
}
```

* 실행 결과

```text
자바 Optional 객체
```

또한, 다음과 같은 메소드를 사용하여 null 대신에 대체할 값을 지정할 수도 있다.

* `orElse()` 메소드: 저장된 값이 존재하면 그 값을 반환하고, 값이 존재하지 않으면 인수로 전달된 값을 반환함

* `orElseGet()` 메소드: 저장된 값이 존재하면 그 값을 반환하고, 값이 존재하지 않으면 인수로 전달된 람다 표현식의 결괏값을 반환함

* `orElseThrow()` 메소드: 저장된 값이 존재하면 그 값을 반환하고, 값이 존재하지 않으면 인수로 전달된 예외를 발생시킴

* 예제

```java
Optional<String> opt = Optional.empty(); // Optional를 null로 초기화함

System.out.println(opt.orElse("빈 Optional 객체"));
System.out.println(opt.orElseGet(String::new));
```

* 실행 결과

```text
빈 Optional 객체
```

위의 예제에서 사용된 `empty()` 메소드는 Optional 객체를 null로 초기화해준다.

## 4. 기본 타입의 Optional 클래스
자바에서는 IntStream 클래스와 같이 기본 타입 스트림을 위한 별도의 Optional 클래스를 제공하고 있다.

* OptionalInt 클래스

* OptionalLong 클래스

* OptionalDouble 클래스

이러한 클래스는 반환 타입이 `Optional<T>` 타입이 아니라 해당 기본 타입이라는 사실만 제외하면 거의 모든 면에서 비슷하다.

또한, Optional 객체에서 `get()` 메소드를 사용하여 저장된 값에 접근할 수 있는 것처럼 클래스별로 저장된 값에 접근할 수 있는 다음과 같은 메소드를 제공하고 있다.

| 클래스 | 저장된 값에 접근하는 메소드 |
|:-----:|:-----:|
| `Optional<T>` | T get() |
| `OptionalInt` | int getAsInt() |
| `OptionalLong` | long getAsLong() |
| `OptionalDouble` | double getAsDouble() |

* 예제

```java
IntStream stream = IntStream.of(4, 2, 1, 3);
OptionalInt result = stream.findFirst();

System.out.println(result.getAsInt());
```

* 실행 결과

```text
4
```

## 5. Optional 메소드
Optional 클래스의 메소드는 다음과 같다.

| 메소드 | 설명 |
|:-----:|:-----:|
| `static <T> Optional<T> empty()` | 아무런 값도 가지지 않는 비어있는 Optional 객체를 반환함 |
| `T get()` | Optional 객체에 저장된 값을 반환함 |
| `boolean isPresent()` | 저장된 값이 존재하면 true를 반환하고, 값이 존재하지 않으면 false를 반환함 |
| `static <T> Optional<T> of(T value)` | null이 아닌 명시된 값을 가지는 Optional 객체를 반환함 |
| `static <T> Optional<T> ofNullable(T value)` | 명시된 값이 null이 아니면 명시된 값을 가지는 Optional 객체를 반환하며, 명시된 값이 null이면 비어있는 Optional 객체를 반환함 |
| `T orElse(T other)` | 저장된 값이 존재하면 그 값을 반환하고, 값이 존재하지 않으면 인수로 전달된 값을 반환함 |
| `T orElseGet(Supplier<? extends T> other)` | 저장된 값이 존재하면 그 값을 반환하고, 값이 존재하지 않으면 인수로 전달된 람다 표현식의 결괏값을 반환함 |
| `<X extends Throwable> T orElseThrow(Supplier<? extends X>  exceptionSupplier)` | 저장된 값이 존재하면 그 값을 반환하고, 값이 존재하지 않으면 인수로 전달된 예외를 발생시킴 |

## [출처 및 참고]
* [https://www.tcpschool.com/java/java_stream_optional](https://www.tcpschool.com/java/java_stream_optional)
