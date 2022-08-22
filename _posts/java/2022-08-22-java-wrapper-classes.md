---
title: Java Wrapper Classes
author: dejavuhyo
date: 2022-08-22 09:30:00 +0900
categories: [Language, Java]
tags: [java-wrapper-classes, wrapper-classes, autoboxing, unboxing, 자바-래퍼-클래스, 래퍼-클래스, 오토박싱, 언박싱]
---

## 1. Wrapper Classes
이름에서 알 수 있듯이 래퍼 클래스는 원시 Java 유형을 캡슐화하는 객체이다. 각 Java 프리미티브(primitive)에는 해당 래퍼가 있다.

* boolean, byte, short, char, int, long, float, double

* Boolean, Byte, Short, Character, Integer, Long, Float, Double

이것들은 모두 `java.lang` 패키지에 정의되어 있으므로 수동으로 가져올 필요가 없다.

기본적으로 제네릭 클래스는 객체에서만 작동하며 기본 요소를 지원하지 않는다. 결과적으로 우리가 그것들로 작업하고 싶다면 원시 값을 래퍼 객체로 변환해야 한다.

예를 들어, Java Collection Framework는 객체와 독점적으로 작동한다. 오래전(Java 5 이전)에는 오토박싱이 없었고, 예를 들어 `Integers` 컬렉션에 대해 단순히 `add(5)`를 호출할 수 없었다. 그 당시에는 이러한 원시 값을 해당 래퍼 클래스로 수동으로 변환하고 컬렉션에 저장해야 했다.

현재 autoboxing을 사용하면 `ArrayList.add(101)` 를 쉽게 수행할 수 있지만 내부적으로 Java는 `valueOf()` 메서드를 사용하여 ArrayList에 저장하기 전에 기본 값을 Integer로 변환한다.

## 2. 원시에서 래퍼 클래스로의 변환
원시 값을 해당 래퍼 클래스(예: `int`에서 `Integer`로 또는 `char`에서 `Character`로 변환하는 방법)로 변환하는 것이다.

생성자 또는 정적 팩토리 메소드를 사용하여 원시 값을 래퍼 클래스의 객체로 변환할 수 있다.

그러나 Java 9부터 `Integer` 또는 `Long`과 같은 많은 boxed 프리미티브에 대한 생성자는 더 이상 사용되지 않는다.

따라서 새 코드에는 팩토리 메서드만 사용하는 것이 좋다.

Java에서 `int` 값을 `Integer` 객체로 변환하는 예이다.

```java
Integer object = new Integer(1);

Integer anotherObject = Integer.valueOf(1);
```

`valueOf()` 메서드는 지정된 int 값을 나타내는 인스턴스를 반환한다.

효율적으로 만드는 캐시된 값을 반환한다. 항상 `-128`에서 `127` 사이의 값을 캐시하지만 이 범위 밖의 다른 값도 캐시할 수 있다.

마찬가지로 `boolean`을 `Boolean`으로, `byte`를 `Byte`로, `char`를 `Character`로, `long`을 `Long`으로, `float`를 `Float`로, `double`을 `Double`로 변환할 수도 있다. `String`을 `Integer`로 변환 해야 하는 경우 String이 래퍼 클래스가 아니기 때문에 `parseInt()` 메서드를 사용해야 한다.

반면에 래퍼 객체에서 기본 값으로 변환하려면 `intValue()`, `doubleValue()` 등과 같은 해당 메서드를 사용할 수 있다.

```java
int val = object.intValue();
```

## 3. 오토박싱과 언박싱
Java 5 이후에는 `autoboxing` 및 `unboxing`이라는 기능을 사용하여 이 변환을 자동으로 수행할 수 있다.

`Boxing`은 기본 값을 해당 래퍼 객체로 변환하는 것을 말한다. 이것은 자동으로 발생할 수 있기 때문에 오토박싱이라고 한다.

유사하게, 래퍼 객체가 기본 값으로 언래핑되면 이것을 언박싱(unboxing)이라고 한다.

이것이 실제로 의미하는 바는 래퍼 객체를 기대하는 메소드에 프리미티브 값을 전달하거나 객체를 기대하는 변수에 프리미티브를 할당할 수 있다는 것이다.

```java
List<Integer> list = new ArrayList<>();
list.add(1); // autoboxing

Integer val = 2; // autoboxing
```

Java는 기본 int 값을 래퍼로 자동 변환한다.

내부적으로 변환을 용이하게 하기 위해 `valueOf()` 메서드를 사용한다. 예를 들어 다음 행은 동일하다.

```java
Integer value = 3;

Integer value = Integer.valueOf(3);
```

이렇게 하면 변환이 쉽고 코드가 더 읽기 쉬워지지만 루프 내부와 같이 오토박싱을 사용하지 않아야 하는 경우가 있다.

오토박싱과 유사하게, 프리미티브를 기대하는 메소드에 객체를 전달할 때 또는 프리미티브 변수에 할당할 때 언박싱이 자동으로 수행된다.

```java
Integer object = new Integer(1); 
int val1 = getSquareValue(object); //unboxing
int val2 = object; //unboxing

public static int getSquareValue(int i) {
    return i*i;
}
```

기본적으로 기본 값이나 래퍼 객체를 허용하는 메서드를 작성하면 두 값을 모두 전달할 수 있다. Java는 컨텍스트에 따라 프리미티브 또는 래퍼와 같은 올바른 유형을 전달한다.

## [출처 및 참고]
* <https://www.baeldung.com/java-wrapper-classes>
