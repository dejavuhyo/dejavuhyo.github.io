---
title: Java 인터페이스
author: dejavuhyo
date: 2021-08-31 06:00:00 +0900
categories: [Language, Java]
tags: [java-interface, interface, 자바-인터페이스, 인터페이스]
---

## 1. 인터페이스란
인터페이스(interface)는 자바 프로그래밍 언어에서 클래스들이 구현해야 하는 동작을 지정하는 데 사용되는 추상 자료형이다.

이들은 프로토콜과 비슷하다. 인터페이스는 interface라는 키워드를 사용하여 선언하며, 메소드 시그너처와 상수 선언(static과 final이 둘 다 선언되는 변수 선언)만을 포함할 수 있다.

자바 8 미만의 모든 버전을 기준으로 인터페이스의 모든 메소드는 구현체(메소드 바디)를 포함하고 있지 않다. 자바 8부터, default와 static 메소드는 interface 정의에 구현체를 가지고 있을 수 있다.

## 2. 장점

* Cooperation (협업)

* Easy to replace (교체 용이)

* Multiple inheritance (다중 상속)

* 개발 시간 단축

* 표준화 가능

* 관계없는 클래스들에게 관계를 맺어 줄 수 있음

* 독립적인 프로그래밍 가능

## 3. 사용법

### 1) 인터페이스 정의
인터페이스들은 다음의 구문을 사용하여 정의된다.

```text
[visibility] interface InterfaceName [extends other interfaces] {
    constant declarations
    abstract method declarations
}
```

인터페이스의 바디(body)는 추상 메소드를 포함하고 있지만 인터페이스 내의 모든 메소드들이 정의상 추상적이기 때문에 abstract 키워드가 필요하지 않다.

인터페이스는 노출된 행동들의 집합을 규정하므로 모든 메소드들은 암묵적으로 public이다.

그러므로 단순한 인터페이스는 다음과 같이 될 수 있다.

```java
public interface Predator {
    boolean chasePrey(Prey p);
    void eatPrey(Prey p);
}
```

### 2) 클래스에서의 인터페이스 구현
인터페이스 구현 문법은 다음 공식을 사용한다.

```text
... implements InterfaceName[, another interface, another, ...] ...
```

클래스는 인터페이스를 구현할 수 있다. 예를들면 다음과 같다.

```java
public class Lion implements Predator {

    @Override
    public boolean chasePrey(Prey p) {
        // programming to  chase prey p (specifically for a lion)
    }
    
    @Override
    public void eatPrey(Prey p) {
        // programming to eat prey p (specifically for a lion)
    }
}
```

클래스가 인터페이스를 구현하면서 메소드를 모두 구현하지 않는다면, abstract로 표시되어야 한다.

클래스가 추상적이라면 서브 클래스들 중 하나가 미구현 메소드를 구현할 것으로 예측한다. 추상 클래스의 서브 클래스들 중 어느 것도 인터페이스 메소드를 모두 구현하지 않더라도 서브 클래스 자체는 abstract로 다시 표시되어야 한다.

클래스들은 여러 인터페이스를 구현할 수 있다.

```java
public class Frog implements Predator, Prey { ... }
```

인터페이스들은 일반적으로 콜백을 목적으로 자바 언어에서 사용된다. 자바는 메소드(프로시저)를 인수(argument)로 전달하는 것을 허용하지 않는다.

### 3) 서브 인터페이스
인터페이스들은 아래와 같은 동일 공식을 사용하여 기타 여러 인터페이스를 확장(extend)할 수 있다. 다음은 서브 인터페이스를 정의한다.

```java
public interface VenomousPredator extends Predator, Venomous {
    //interface body
 }
```

## [출처 및 참고]
* [https://gofnrk.tistory.com/22](https://gofnrk.tistory.com/22)
* [https://bywords.tistory.com/entry/Java-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%9D%98-%EC%9E%A5%EC%A0%90](https://bywords.tistory.com/entry/Java-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%9D%98-%EC%9E%A5%EC%A0%90)
