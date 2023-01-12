---
title: Java Concrete 클래스
author: dejavuhyo
date: 2021-12-02 09:30:00 +0900
categories: [Language, Java]
tags: [java-concrete-class, concrete-class, 자바-구체-클래스, 자바-구체적인-클래스, 자바-클래스]
---

## 1.구체 클래스란
구체적인 클래스는 new 키워드를 사용하여 인스턴스를 만들 수 있는 클래스이다 .

즉, 청사진의 완전한 구현이다.

Car 클래스 예이다.

```java
public class Car {
    public String honk() {
        return "beep!";
    }

    public String drive() {
        return "vroom";
    }
}
```

모든 메서드가 구현되어 있으므로 이를 구체 클래스라고 부르며 인스턴스화할 수 있다.

```java
Car car = new Car();
```

JDK의 구체적인 클래스의 몇 가지 예는 HashMap, HashSet, ArrayList 및 LinkedList 이다.

## 2. 자바 추상화와 구체 클래스
모든 Java 유형이 모든 메소드를 구현하는 것은 아니다. 추상화 라고도 하는 이러한 유연성을 통해 모델링하려는 도메인에 대해 보다 일반적인 용어로 생각할 수 있다.

Java에서는 인터페이스와 추상 클래스를 사용하여 추상화를 달성할 수 있다.

이러한 다른 클래스와 비교하여 구체적인 클래스를 비교해 본다.

### 1) Interfaces
인터페이스는 클래스의 청사진이다. 즉, 구현되지 않은 메서드 서명 모음이다.

```java
interface Driveable {
    void honk();
    void drive();
}
```

class 대신 interface 키워드를 사용한다.

때문에 Driveable가 구현되지 않은 방법을 가지고, 우리는 그것을 new 키워드로 인스턴스화 할 수 없다.

그러나 Car와 같은 구체적인 클래스는 이러한 메서드를 구현할 수 있다.

JDK는 Map, List 및 Set과 같은 여러 인터페이스를 제공한다.

### 2) Abstract Classes
추상 클래스는 구현되지 않은 메서드가 있는 클래스 이지만 실제로는 다음 두 가지를 모두 가질 수 있다.

```java
public abstract class Vehicle {
    public abstract String honk();

    public String drive() {
        return "zoom";
    }
}
```

abstract 키워드로 추상 클래스를 표시한다는 점에 유의해야 한다.

다시, Vehicle에는 구현되지 않은 메서드 honk가 있으므로 new 키워드를 사용할 수 없다.

JDK에서 추상 클래스의 몇 가지 예는 AbstractMap 및 AbstractList 이다.

### 3) Concrete Classes
대조적으로, 구체적인 클래스에는 구현되지 않은 메서드가 없다. 구현이 상속되는지 여부에 관계없이 각 메서드에 구현이 있는 한 클래스는 구체적이다.

구체 클래스는 앞의 Car 예제 처럼 간단할 수 있다. 인터페이스를 구현하고 추상 클래스를 확장할 수도 있다.

```java
public class FancyCar extends Vehicle implements Driveable {
    public String honk() { 
        return "beep";
    }
}
```

FancyCar 클래스는 경적을 위한 구현을 제공하며 차량의 주행 구현을 이어받는다.

따라서 구현되지 않은 메서드가 없다. 따라서 new 키워드로 FancyCar 클래스 인스턴스를 생성할 수 있다.

```java
FancyCar car = new FancyCar();
```

간단히 말해서 추상이 아닌 모든 클래스를 구체적인 클래스라고 부를 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-concrete-class](https://www.baeldung.com/java-concrete-class)
