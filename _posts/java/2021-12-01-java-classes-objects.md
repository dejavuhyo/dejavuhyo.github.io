---
title: Java 클래스와 객체
author: dejavuhyo
date: 2021-12-01 09:30:00 +0900
categories: [Language, Java]
tags: [java-classes-objects, classes-objects, java-class, java-object, 자바-클래스-객체, 클래스-객체, 자바-클래스, 자바-객체]
---

## 1. 클래스와 객체
Java 프로그래밍 언어의 두 가지 기본 빌딩 블록은 클래스와 객체이다. 실제 엔티티를 모델링하는데 사용하는 객체 지향 프로그래밍(OOP)의 기본 개념이다.

OOP에서 클래스는 객체에 대한 청사진 또는 템플릿이다. 엔티티 유형을 설명하는데 사용한다.

반면에 객체는 클래스에서 생성된 살아있는 개체이다. 그들은 필드 내에 특정 상태를 포함하고 메서드와 함께 특정 동작을 나타낸다.

## 2. Classes
간단히 말해서, 클래스는 객체의 정의 또는 유형을 나타낸다. Java에서 클래스는 필드, 생성자 및 메소드를 포함할 수 있다.

Car를 나타내는 간단한 Java 클래스를 사용하는 예이다.

```java
class Car {

    // fields
    String type;
    String model;
    String color;
    int speed;

    // constructor
    Car(String type, String model, String color) {
        this.type = type;
        this.model = model;
        this.color = color;
    }
    
    // methods
    int increaseSpeed(int increment) {
        this.speed = this.speed + increment;
        return this.speed;
    }
    
    // ...
}
```

이 Java 클래스는 일반적으로 자동차를 나타낸다. 이 클래스에서 모든 유형의 자동차를 만들 수 있다. 필드를 사용하여 상태를 유지하고 생성자를 사용하여 이 클래스에서 개체를 만든다.

모든 Java 클래스에는 기본적으로 빈 생성자가 있다. 위에서 했던 것처럼 특정 구현을 제공하지 않는 경우 사용한다. 기본 생성자가 Car 클래스를 찾는 방법은 다음과 같다.

```java
Car(){}
```

이 생성자는 단순히 개체의 모든 필드를 기본값으로 초기화한다. 문자열은 null로 초기화 되고 정수는 0으로 초기화 된다.

클래스에는 특정 생성자가 있다. 왜냐하면 객체를 생성할 때 객체에 필드가 정의되기를 원하기 때문이다.

```java
Car(String type, String model) {
    // ...
}
```

요약하면, 자동차를 정의하는 클래스를 작성했다. 그 속성은 클래스의 개체 상태를 포함하는 필드로 설명되고, 그 동작은 메서드를 사용하여 설명된다.

## 3. Objects
클래스는 컴파일 시간에 변환되지만 개체는 런타임에 클래스에서 생성된다.

클래스의 객체를 인스턴스라고 하며 생성자를 사용하여 객체를 생성하고 초기화한다.

```java
Car focus = new Car("Ford", "Focus", "red");
Car auris = new Car("Toyota", "Auris", "blue");
Car golf = new Car("Volkswagen", "Golf", "green");
```

단일 클래스에서 모두 다른 Car 객체를 만들었다. 한 곳에서 청사진을 정의한 다음 여러 곳에서 여러 번 재사용하는 것이 모든 것의 요점이다.

지금까지 세 개의 Car 개체가 있으며 속도가 0이므로 모두 주차되어 있다. 증가 속도 메소드를 호출하여 이것을 변경할 수 있다.

```java
focus.increaseSpeed(10);
auris.increaseSpeed(20);
golf.increaseSpeed(30);
```

자동차의 상태를 변경했다. 모두 다른 속도로 움직이고 있다.

게다가 클래스, 생성자, 필드, 메서드에 대한 액세스 제어를 정의할 수 있고 정의해야 한다. 액세스 한정자를 사용하여 그렇게 할 수 있다.

## 4. 액세스 수정자
이전 예제에서는 코드를 단순화하기 위해 액세스 수정자를 생략했다. 그렇게 함으로써 실제로 기본 package-private 수정자를 사용했다. 해당 수정자는 동일한 패키지에 있는 다른 클래스의 클래스에 대한 액세스를 허용한다.

일반적으로 다른 모든 객체에서 액세스를 허용하기 위해 생성자에 대해 public 수정자를 사용 한다.

```java
public Car(String type, String model, String color) {
    // ...
}
```

클래스의 모든 필드와 메서드는 또한 특정 한정자에 의해 액세스 제어를 정의해야 한다. 클래스에는 일반적으로 public 수정자가 있지만 필드를 private으로 유지하는 경향이 있다.

필드는 객체의 상태를 유지하므로 해당 상태에 대한 액세스를 제어하려고 한다. 그들 중 일부를 비공개로 유지 하고 다른 일부를 공개 할 수 있다. getter 및 setter라는 특정 메서드를 사용하여 이를 달성한다.

완전히 지정된 액세스 제어가 있는 클래스를 살펴본다.

```java
public class Car {
    private String type;
    // ...

    public Car(String type, String model, String color) {
       // ...
    }

    public String getColor() {
        return color;
    }

    public void setColor(String color) {
        this.color = color;
    }

    public int getSpeed() {
        return speed;
    }

    // ...
}
```

클래스는 public으로 표시되어 있어 모든 패키지에서 사용할 수 있다. 또한 생성자는 public이므로 이 클래스에서 다른 객체 내부의 객체를 생성할 수 있다.

필드는 private로 표시되어 있다. 즉, 개체에서 직접 액세스할 수 없지만 getter와 setter를 통해 액세스할 수 있다.

유형과 모델들이 우리의 객체의 내부 데이터를 보유하기 때문에 필드는 getter와 setter가 없다. 초기화하는 동안 생성자를 통해서만 정의할 수 있다.

또한 색상은 액세스 및 변경할 수 있는 반면 속도는 액세스만 가능하지만 변경할 수는 없다. 우리는 전문화된 공개 메소드인 growthSpeed() 및 reductionSpeed()를 통해 속도 조정을 시행했다.

즉, 액세스 제어를 사용하여 개체의 상태를 캡슐화한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-classes-objects](https://www.baeldung.com/java-classes-objects)
