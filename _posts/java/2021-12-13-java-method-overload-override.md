---
title: Java 메소드 오버로딩 및 오버라이드
author: dejavuhyo
date: 2021-12-13 09:30:00 +0900
categories: [Language, Java]
tags: [java-method-overload-override, java-overload-override, method-overload-override, overload-override, method-overload, method-override, java-메소드-오버로딩-오버라이드, 메소드-오버로딩-오버라이드, 오버로딩-오버라이드, 메소드-오버로딩, 메소드-오버라이드, 자바-오버로딩-오버라이드]
---

## 1. 메소드 오버로딩
메서드 오버로딩은 응집력 있는 클래스 API를 정의할 수 있는 강력한 메커니즘이다.

두 개의 숫자, 세 개의 숫자 등을 곱하는 다른 방법을 구현하는 단순한 유틸리티 클래스를 작성했다고 가정합니다.

multiply2(), multiply3(), multiply4()와 같이 오해의 소지가 있거나 모호한 이름을 메서드에 부여했다면 이는 잘못 설계된 클래스 API가 될 것이다. 여기에서 메서드 오버로딩이 작동한다.

간단히 말해서, 두 가지 다른 방법으로 메소드 오버로딩을 구현할 수 있다.

* 이름은 같지만 인수 개수가 다른 두 개 이상의 메서드 구현

* 이름은 같지만 다른 유형의 인수를 사용하는 두 개 이상의 메서드 구현

### 1) 다른 수의 인수
Multiplier 클래스는 간단히 말해서, 서로 다른 인수의 수를 취하는 두 개의 구현을 정의함으로써 multiply() 메소드를 과부하시키는 방법을 보여준다.

```java
public class Multiplier {
    
    public int multiply(int a, int b) {
        return a * b;
    }
    
    public int multiply(int a, int b, int c) {
        return a * b * c;
    }
}
```

### 2) 다른 유형의 인수
마찬가지로, 우리는 multiply() 방법을 다른 종류의 인수를 받아들이게 함으로써 과부하를 일으킬 수 있다.

```java
public class Multiplier {
    
    public int multiply(int a, int b) {
        return a * b;
    }
    
    public double multiply(double a, double b) {
        return a * b;
    }
}
```

또한 두 가지 유형의 메서드 오버로딩으로 Multiplier 클래스를 정의하는 것이 적절하다.

```java
public class Multiplier {
    
    public int multiply(int a, int b) {
        return a * b;
    }
    
    public int multiply(int a, int b, int c) {
        return a * b * c;
    }
    
    public double multiply(double a, double b) {
        return a * b;
    }
}
```

그러나 반환 유형만 다른 두 가지 메서드를 구현할 수는 없다.

그 이유를 이해하기 위해 다음 예를 살펴본다.

```java
public int multiply(int a, int b) {
    return a * b; 
}
 
public double multiply(int a, int b) {
    return a * b; 
}
```

이 경우 코드는 메서드 호출의 모호성 때문에 컴파일 되지 않을 것이다. 컴파일러는 호출 할 multiply()의 구현을 알지 못할 것 이다.

### 3) 유형 프로모션
메소드 오버로딩이 제공하는 깔끔한 기능 중 하나는 이른바 유형 승격(확장 기본 변환) 이다.

간단히 말해서, 오버로드된 메서드와 특정 메서드 구현에 전달된 인수의 형식이 일치하지 않는 경우 지정된 형식이 다른 형식으로 암시적으로 승격된다.

유형 승격이 작동하는 방식을 더 명확하게 이해하려면 다음과 같은 multiply() 메서드 구현을 고려해야 한다.

```java
public double multiply(int a, long b) {
    return a * b;
}

public int multiply(int a, int b, int c) {
    return a * b * c;
}
```

이제 두 개의 int 인수로 메서드를 호출하면 두 번째 인수가 long으로 승격 된다. 이 경우 두 개의 int 인수가 있는 메서드의 일치하는 구현이 없기 때문이다.

유형 승격을 보여주기 위한 빠른 단위 테스트를 살펴보겠습니다.

```java
@Test
public void whenCalledMultiplyAndNoMatching_thenTypePromotion() {
    assertThat(multiplier.multiply(10, 10)).isEqualTo(100.0);
}
```

반대로, 일치하는 구현으로 메서드를 호출하면 유형 승격이 발생하지 않는다.

```java
@Test
public void whenCalledMultiplyAndMatching_thenNoTypePromotion() {
    assertThat(multiplier.multiply(10, 10, 10)).isEqualTo(1000);
}
```

다음은 메서드 오버로딩에 적용되는 형식 승격 규칙의 요약이다.

* byte는 short, int, long, float 또는 double로 승격될 수 있다.

* short는 int, long, float 또는 double로 승격될 수 있다.

* char는 int, long, float 또는 double로 승격될 수 있다.

* int는 long, float 또는 double로 승격될 수 있다.

* long은 float 또는 double로 승격될 수 있다.

* float는 double로 승격될 수 있다.

### 4) 정적 바인딩
특정 메서드 호출을 메서드 본문에 연결하는 기능을 바인딩이라고 한다.

메서드 오버로딩의 경우 바인딩은 컴파일 타임에 정적으로 수행되므로 정적 바인딩이라고 한다.

컴파일러는 단순히 메서드의 서명을 확인하여 컴파일 타임에 바인딩을 효과적으로 설정할 수 있다.

## 2. 메소드 오버라이드
메서드 재정의를 사용하면 기본 클래스에 정의된 메서드에 대해 하위 클래스에서 세분화된 구현을 제공할 수 있다.

메소드 오버라이딩이 강력한 기능이지만, 상속 사용의 논리적 결과라는 점을 고려할 때 OOP의 가장 큰 요소 중 하나는 사용 사례별로 신중하게 분석해야 한다.

간단한 상속 기반("is-a") 관계를 만들어 메서드 재정의를 사용하는 방법을 살펴본다.

기본 클래스는 다음과 같다.

```java
public class Vehicle {
    
    public String accelerate(long mph) {
        return "The vehicle accelerates at : " + mph + " MPH.";
    }
    
    public String stop() {
        return "The vehicle has stopped.";
    }
    
    public String run() {
        return "The vehicle is running.";
    }
}
```

다음은 고안된 하위 클래스이다.

```java
public class Car extends Vehicle {

    @Override
    public String accelerate(long mph) {
        return "The car accelerates at : " + mph + " MPH.";
    }
}
```

위의 계층 구조에서 하위 유형 Car에 대한 보다 세련된 구현을 제공하기 위해 단순히 accelerate() 메서드를 재정의했다.

여기서 응용 프로그램이 차량 클래스의 인스턴스를 사용하는 경우 accelerate() 메서드의 두 구현의 서명과 반환 유형이 동일하므로 Car의 인스턴스에서도 작동할 수 있음을 알 수 있다.

Vehicle 및 Car클래스를 확인하기 위해 몇 가지 단위 테스트를 작성한다.

```java
@Test
public void whenCalledAccelerate_thenOneAssertion() {
    assertThat(vehicle.accelerate(100))
      .isEqualTo("The vehicle accelerates at : 100 MPH.");
}
    
@Test
public void whenCalledRun_thenOneAssertion() {
    assertThat(vehicle.run())
      .isEqualTo("The vehicle is running.");
}
    
@Test
public void whenCalledStop_thenOneAssertion() {
    assertThat(vehicle.stop())
      .isEqualTo("The vehicle has stopped.");
}

@Test
public void whenCalledAccelerate_thenOneAssertion() {
    assertThat(car.accelerate(80))
      .isEqualTo("The car accelerates at : 80 MPH.");
}
    
@Test
public void whenCalledRun_thenOneAssertion() {
    assertThat(car.run())
      .isEqualTo("The vehicle is running.");
}
    
@Test
public void whenCalledStop_thenOneAssertion() {
    assertThat(car.stop())
      .isEqualTo("The vehicle has stopped.");
}
```

재정의되지 않은 run() 및 stop() 메서드가 Car와 Vehicle 모두에 대해 동일한 값을 반환 하는 방법을 보여주는 몇 가지 단위 테스트이다.

```java
@Test
public void givenVehicleCarInstances_whenCalledRun_thenEqual() {
    assertThat(vehicle.run()).isEqualTo(car.run());
}
 
@Test
public void givenVehicleCarInstances_whenCalledStop_thenEqual() {
   assertThat(vehicle.stop()).isEqualTo(car.stop());
}
```

두 클래스의 소스 코드에 액세스할 수 있으므로 기본 Vehicle 인스턴스에서 accelerate() 메서드를 호출 하고 Car 인스턴스에서 accelerate()을 호출 하면 동일한 인수에 대해 다른 값이 반환 된다는 것을 분명히 알 수 있다.

따라서 다음 테스트는 Car 인스턴스에 대해 재정의된 메서드가 호출됨을 보여준다.

```java
@Test
public void whenCalledAccelerateWithSameArgument_thenNotEqual() {
    assertThat(vehicle.accelerate(100))
      .isNotEqualTo(car.accelerate(100));
}
```

### 1) 유형 대체 가능성
OOP의 핵심 원칙은 LSP(Liskov Substitution Principle)와 밀접하게 관련된 유형 대체 가능성 이다.

간단히 말해서, LSP는 응용 프로그램이 주어진 기본 유형과 함께 작동하면 모든 하위 유형과도 작동해야 한다고 명시한다. 그렇게 하면 형식 대체 가능성이 적절하게 보존된다.

메서드 재정의의 가장 큰 문제는 파생 클래스의 일부 특정 메서드 구현이 LSP를 완전히 준수하지 않아 형식 대체 가능성을 유지하지 못할 수 있다는 것이다.

물론 다른 유형의 인수를 허용하고 다른 유형도 반환하도록 재정의된 메서드를 만드는 것이 유효하지만 다음 규칙을 완전히 준수해야 한다.

* 기본 클래스의 메소드가 주어진 유형의 인수를 사용하는 경우 재정의된 메소드는 동일한 유형 또는 상위 유형(일명 반공변 메소드 인수)을 취해야 한다.

* 기본 클래스의 메서드가 void를 반환 하면 재정의된 메서드는 void를 반환해야 한다.

* 기본 클래스의 메소드가 프리미티브를 리턴하면 재정의된 메소드는 동일한 프리미티브를 리턴해야 한다.

* 기본 클래스의 메서드가 특정 형식을 반환하는 경우 재정의된 메서드는 동일한 형식 또는 하위 형식(일명 공변 반환 형식)을 반환해야 한다.

* 기본 클래스의 메서드가 예외를 throw하는 경우 재정의된 메서드는 동일한 예외 또는 기본 클래스 예외의 하위 유형을 throw해야 한다.

### 2) 동적 바인딩
메소드 오버라이드는 기본 유형과 하위 유형의 계층이 있는 상속에서만 구현될 수 있다는 점을 고려하면 컴파일러는 컴파일 시간에 어떤 메소드를 호출할지 결정할 수 없다.

결과적으로 컴파일러는 어떤 메서드를 호출해야 하는지 알기 위해 객체의 유형을 확인해야 한다.

이 검사는 런타임에 발생하므로 메서드 재정의는 동적 바인딩의 일반적인 예이다.

## [출처 및 참고]
* [https://www.baeldung.com/java-method-overload-override](https://www.baeldung.com/java-method-overload-override)
