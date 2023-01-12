---
title: Java 상속
author: dejavuhyo
date: 2021-12-08 10:00:00 +0900
categories: [Language, Java]
tags: [java-inheritance, inheritance, 자바-상속, 상속, 클래스-상속, 다중-상속]
---

## 1. 상속이란
객체 지향 프로그래밍의 핵심 원칙 중 하나인 상속을 통해 기존 코드를 재사용하거나 기존 유형을 확장할 수 있다.

간단히 말해서 Java에서 클래스는 다른 클래스와 여러 인터페이스를 상속할 수 있는 반면 인터페이스는 다른 인터페이스를 상속할 수 있다.

## 2. 상속의 필요성
자동차 제조업체가 고객에게 여러 자동차 모델을 제공한다고 상상해 본다. 자동차 모델에 따라 선루프나 방탄창과 같은 기능이 다를 수 있지만 엔진 및 휠과 같은 공통 구성 요소와 기능이 모두 포함된다.

처음부터 차종별로 따로 디자인하는 것보다 기본 디자인을 만들고 확장해 특화된 버전 을 만드는 것이 합리적 이다.

비슷한 방식으로 상속을 통해 기본 기능과 동작을 가진 클래스를 만들고 이 기본 클래스를 상속하는 클래스를 만들어 특수 버전을 만들 수 있다. 같은 방식으로 인터페이스는 기존 인터페이스를 확장할 수 있다.

다른 유형, 특히 다음과 같이 다른 유형에 의해 상속된 유형을 참조하기 위해 여러 용어를 사용하는 것을 알 수 있다.

* 기본 유형은 수퍼 또는 상위 유형이라고도 한다.

* 파생 유형은 확장, 하위 또는 하위 유형이라고 한다.

## 3. 클래스 상속

### 1) 클래스 확장
클래스는 다른 클래스를 상속하고 추가 멤버를 정의할 수 있다.

Car 기본 클래스를 정의한다.

```java
public class Car {
    int wheels;
    String model;
    void start() {
        // Check essential parts
    }
}
```

클래스 ArmoredCar는 다음과 같은 선언에서 키워드 확장을 사용하여 Car 클래스의 멤버를 상속할 수 있다.

```java
public class ArmoredCar extends Car {
    int bulletProofWindows;
    void remoteStartCar() {
        // this vehicle can be started by using a remote control
    }
}
```

이제 ArmoredCar 클래스는 Car의 하위 클래스이고 후자는 ArmoredCar의 상위 클래스라고 말할 수 있다.

Java의 클래스는 단일 상속을 지원한다. ArmoredCar의 클래스는 여러 클래스를 확장 할 수 없다.

또한 extends 키워드가 없는 경우 클래스는 암시적으로 java.lang.Object 클래스를 상속 한다.

하위 클래스 클래스는 상위 클래스 클래스에서 비정적 보호 및 공개 멤버를 상속한다. 또한 두 클래스가 동일한 패키지에 있는 경우 기본 및 패키지 액세스 권한이 있는 멤버가 상속된다.

반면에 클래스의 private 및 static 멤버는 상속되지 않는다.

### 2) 자식 클래스에서 부모 멤버 액세스
상속된 속성이나 메서드에 액세스하려면 간단히 직접 사용할 수 있다.

```java
public class ArmoredCar extends Car {
    public String registerModel() {
        return model;
    }
}
```

멤버에 액세스하기 위해 수퍼 클래스에 대한 참조가 필요하지 않는다.

## 4. 인터페이스 상속

### 1) 다중 인터페이스 구현
클래스는 하나의 클래스만 상속할 수 있지만 여러 인터페이스를 구현할 수 있다.

위에서 정의한 ArmoredCar가 슈퍼 스파이에게 필요하다고 가정한다. 그래서 자동차 제조 회사는 비행 및 플로팅 기능을 추가하는 방법을 생각했다.

```java
public interface Floatable {
    void floatOnWater();
}
```

```java
public interface Flyable {
    void fly();
}
```

```java
public class ArmoredCar extends Car implements Floatable, Flyable{
    public void floatOnWater() {
        System.out.println("I can float!");
    }
 
    public void fly() {
        System.out.println("I can fly!");
    }
}
```

위의 예에서 인터페이스에서 상속 하기 위해 키워드 implements를 사용하는 것을 알 수 있다.

### 2) 다중 상속 문제
Java는 인터페이스를 사용하여 다중 상속을 허용한다.

Java 7까지는 이것이 문제가 되지 않았다. 인터페이스는 추상 메서드, 즉 구현이 없는 메서드만 정의할 수 있다. 따라서 클래스가 동일한 메서드 서명으로 여러 인터페이스를 구현하더라도 문제가 되지 않았다. 구현 클래스는 결국 구현해야 할 단 하나의 메서드를 갖게 되었다.

Java 8과 함께 인터페이스에 기본 메소드가 도입 되었다.

Java 8부터 인터페이스는 해당 메소드에 대한 기본 구현을 정의하도록 선택할 수 있다 (인터페이스는 여전히 추상 메소드를 정의할 수 있음). 즉, 클래스가 동일한 서명으로 메서드를 정의하는 여러 인터페이스를 구현하는 경우 자식 클래스는 별도의 구현을 상속한다. 이것은 복잡하게 들리며 허용되지 않는다.

Java는 별도의 인터페이스에 정의된 동일한 메소드의 다중 구현 상속을 허용하지 않는다.

다음은 예이다.

```java
public interface Floatable {
    default void repair() {
        System.out.println("Repairing Floatable object");
    }
}
```

```java
public interface Flyable {
    default void repair() {
        System.out.println("Repairing Flyable object");
    }
}
```

```java
public class ArmoredCar extends Car implements Floatable, Flyable {
    // this won't compile
}
```

두 인터페이스를 모두 구현하려면 repair() 메서드를 재정의해야 한다.

앞의 예에서 인터페이스가 같은 이름의 변수를 정의하는 경우(예: duration ), 변수 이름 앞에 인터페이스 이름을 지정하지 않으면 액세스할 수 없다.

```java
public interface Floatable {
    int duration = 10;
}
```

```java
public interface Flyable {
    int duration = 20;
}
```

```java
public class ArmoredCar extends Car implements Floatable, Flyable {
 
    public void aMethod() {
        System.out.println(duration); // won't compile
        System.out.println(Floatable.duration); // outputs 10
        System.out.println(Flyable.duration); // outputs 20
    }
}
```

### 3) 다른 인터페이스를 확장하는 인터페이스
인터페이스는 여러 인터페이스를 확장할 수 있다. 다음은 예이다.

```java
public interface Floatable {
    void floatOnWater();
}
```

```java
interface interface Flyable {
    void fly();
}
```

```java
public interface SpaceTraveller extends Floatable, Flyable {
    void remoteControl();
}
```

인터페이스는 extends 키워드를 사용하여 다른 인터페이스를 상속 한다. 클래스는 키워드 implements를 사용하여 인터페이스를 상속합니다.

## 5. 상속 유형
클래스가 다른 클래스나 인터페이스를 상속할 때 해당 멤버를 상속하는 것과 별개로 해당 유형도 상속한다. 이는 다른 인터페이스를 상속하는 인터페이스에도 적용된다.

이것은 개발자가 구현을 프로그래밍하는 대신 인터페이스(기본 클래스 또는 인터페이스)에 프로그래밍할 수 있도록 하는 매우 강력한 개념 이다.

예를 들어 조직에서 직원이 소유한 자동차 목록을 유지 관리하는 조건을 생각한다. 물론 모든 직원이 다른 자동차 모델을 소유할 수 있다. 그렇다면 어떻게 다른 자동차 인스턴스를 참조하는 방법은 다음과 같다.

```java
public class Employee {
    private String name;
    private Car car;
    
    // standard constructor
}
```

Car의 모든 파생 클래스는 Car 유형을 상속하므로 파생 클래스 인스턴스는 Car 클래스의 변수를 사용하여 참조할 수 있다.

```java
Employee e1 = new Employee("Shreya", new ArmoredCar());
Employee e2 = new Employee("Paul", new SpaceCar());
Employee e3 = new Employee("Pavni", new BMW());
```

## 6. 히든 클래스 멤버

### 1) 숨겨진 인스턴스 구성원
슈퍼클래스와 서브클래스가 같은 이름을 가진 변수나 메서드를 정의 하더라도 둘 다에 액세스할 수 있다. 그러나 변수나 메서드에 this 또는 super 키워드를 접두사로 붙여 Java에 의도를 분명히 해야한다.

이 키워드는 사용되는 인스턴스를 나타낸다. 슈퍼키워드(super keyword)는 부모 클래스 인스턴스를 가리킨다.

```java
public class ArmoredCar extends Car {
    private String model;
    public String getAValue() {
        return super.model;   // returns value of model defined in base class Car
        // return this.model;   // will return value of model defined in ArmoredCar
        // return model;   // will return value of model defined in ArmoredCar
    }
}
```

많은 개발자가 this 및 super 키워드를 사용 하여 참조하는 변수 또는 메서드를 명시적으로 나타낸다. 그러나 모든 구성원과 함께 사용하면 코드가 복잡해 보일 수 있다.

### 2) 숨겨진 정적 멤버
기본 클래스와 하위 클래스가 동일한 이름으로 정적 변수와 메서드를 정의하면 어떻게 되는지와 인스턴스 변수에서와 같이 기본 클래스, 파생 클래스에서 정적 멤버에 접근할 수 있는지 예를 들어 알아본다.

```java
public class Car {
    public static String msg() {
        return "Car";
    }
}
```

```java
public class ArmoredCar extends Car {
    public static String msg() {
        return super.msg(); // this won't compile.
    }
}
```

정적 멤버는 인스턴스가 아닌 클래스에 속한다. 따라서 msg()에서 non-static super 키워드를 사용할 수 없다.

정적 멤버는 클래스에 속하므로 이전 호출을 다음과 같이 수정할 수 있다.

```java
return Car.msg();
```

기본 클래스와 파생 클래스가 모두 동일한 서명을 가진 정적 메서드 msg()를 정의하는 다음 예제를 살펴본다.

```java
public class Car {
    public static String msg() {
        return "Car";
    }
}
```

```java
public class ArmoredCar extends Car {
    public static String msg() {
        return "ArmoredCar";
    }
}
```

호출 방법은 다음과 같다.

```java
Car first = new ArmoredCar();
ArmoredCar second = new ArmoredCar();
```

앞의 코드에서 first.msg()는 "Car"를 출력 하고 second.msg()는 "ArmoredCar"를 출력한다. 호출되는 정적 메시지는 ArmoredCar 인스턴스를 참조하는데 사용되는 변수의 유형에 따라 다르다.

## [출처 및 참고]
* [https://www.baeldung.com/java-inheritance](https://www.baeldung.com/java-inheritance)
