---
title: Java 변수
author: dejavuhyo
date: 2022-01-17 09:25:00 +0900
categories: [Language, Java]
tags: [java-varargs, varargs, 자바-변수, 변수]
---

## 1. Varargs란
Varargs는 Java 5에 도입되었으며 한 유형의 임의의 수의 매개변수를 지원하는 메소드에 대한 약칭을 제공한다.

## 2. Varargs 이전
Java 5 이전에는 임의의 수의 인수를 전달할 때마다 배열의 모든 인수를 전달하거나 N 메서드를 구현해야 했다(각 추가 매개변수에 대해 하나씩).

```java
public String format() { ... }

public String format(String value) { ... }

public String format(String val1, String val2) { ... }
```

## 3. 변수 사용
Varargs는 후드 아래에 배열을 사용하여 임의의 수의 매개변수를 자동으로 처리할 수 있는 새로운 구문을 도입하여 상용구 코드 작성을 방지하는데 도움이 된다.

표준 유형 선언과 생략 부호를 사용하여 정의할 수 있다.

```java
public String formatWithVarArgs(String... values) {
    // ...
}
```

다음과 같이 임의의 수의 인수를 사용하여 메서드를 호출할 수 있다.

```java
formatWithVarArgs();

formatWithVarArgs("a", "b", "c", "d");
```

앞서 언급했듯이 varargs는 배열이므로 일반 배열에서 작업하는 것처럼 varargs로 작업해야 한다.

## 4. 규칙
Varargs는 사용하기 쉽다. 그러나 명심해야 할 몇 가지 규칙이 있다.

* 각 메소드는 varargs 매개변수를 하나만 가질 수 있다.

* 가변 인자 인수는 마지막 매개 변수 여야한다

## 5. Heap Pollution
varargs를 사용하면 소위 [Heap Pollution](https://en.wikipedia.org/wiki/Heap_pollution)이 발생할 수 있다. 힙 오염을 더 잘 이해하려면 다음 varargs 방법을 고려해야한다.

```java
static String firstOfFirst(List<String>... strings) {
    List<Integer> ints = Collections.singletonList(42);
    Object[] objects = strings;
    objects[0] = ints; // Heap pollution

    return strings[0].get(0); // ClassCastException
}
```

테스트에서 이 이상한 방법을 호출한다.

```java
String one = firstOfFirst(Arrays.asList("one", "two"), Collections.emptyList());

assertEquals("one", one);
```

여기에 명시적 형식 캐스트를 사용하지 않았더라도 ClassCastException을 받을 수 있다.

### 1) 안전한 사용
varargs를 사용할 때마다 Java 컴파일러는 주어진 매개변수를 저장할 배열을 생성한다. 이 경우 컴파일러는 인수를 보유할 제네릭 형식 구성 요소가 있는 배열을 만든다.

제네릭 유형과 함께 varargs를 사용할 때 치명적인 런타임 예외의 잠재적 위험이 있으므로 Java 컴파일러는 안전하지 않은 varargs 사용 가능성에 대해 경고 한다.

```text
warning: [varargs] Possible heap pollution from parameterized vararg type T
```

varargs 사용은 다음과 같은 경우에만 안전합니다.

* 암시적으로 생성된 배열에는 아무 것도 저장하지 않는다. 이 예에서는 해당 배열에 List<Integer>를 저장했다.

* 생성된 배열에 대한 참조가 메서드를 탈출하지 못하게 한다.

메소드 자체가 varargs를 안전하게 사용한다고 확신하는 경우 ```@SafeVarargs```를 사용 하여 경고를 억제 할 수 있다.

간단히 말해서 varargs 사용은 호출자에서 메서드로 변수 수의 인수를 전송하는데 사용하는 경우 안전하다.

### 2) Varargs 참조 이스케이프
varargs의 또 다른 안전하지 않은 사용법이다.

```java
static <T> T[] toArray(T... arguments) {
    return arguments;
}
```

처음에는 toArray 메서드가 완전히 무해한 것처럼 보일 수 있다. 그러나 varargs 배열이 호출자에게 이스케이프되도록 하기 때문에 안전한 varargs의 두 번째 규칙을 위반한다.

이 방법이 어떻게 위험할 수 있는지 보려면 다른 방법에서 사용해 본다.

```java
static <T> T[] returnAsIs(T a, T b) {
    return toArray(a, b);
}
```

그런 다음 이 메서드를 호출한다.

```java
String[] args = returnAsIs("One", "Two");
```

다시 ClassCastException이 발생한다. 다음은 returnAsIs 메서드를 호출할때 발생한다.

* a와 b를 toArray 메서드에 전달하려면 Java가 배열을 만들어야 한다.

* ```Object[]```가 어떤 종류의 항목이라도 보유할 수 있기 때문에 컴파일러는 하나를 만든다.

* toArray 메서드는 지정된 ```Object[]```를 호출자에게 반환한다.

* 호출 사이트가 ```String[]```을 예상하기 때문에 컴파일러는``` Object[]```를 예상하고 ```String[]```로 캐스트하려고 시도하므로 ClassCastException이 발생한다.

> heap pollution에 대한 더 자세한 논의는 Joshua Bloch의 [Effective Java](https://www.oreilly.com/library/view/effective-java/9780134686097/) 항목 32를 읽는 것이 좋다.

## [출처 및 참고]
* <https://www.baeldung.com/java-varargs>
