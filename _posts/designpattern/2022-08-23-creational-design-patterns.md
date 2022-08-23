---
title: 창조적인 디자인 패턴
author: dejavuhyo
date: 2022-08-23 10:50:00 +0900
categories: [Language, DesignPattern]
tags: [creational-design-patterns, design-patterns, singleton, factory-method, abstract-factory, builder, 싱글톤, 팩토리-메소드, 추상-팩토리, 빌더, 디자인-패턴, 창조적인-디자인-패턴]
---

## 1. 창조적인 디자인 패턴
창조적인 디자인 패턴은 객체가 생성되는 방식과 관련이 있다. 제어된 방식으로 개체를 생성하여 복잡성과 불안정성을 줄인다.

새 연산자는 응용 프로그램 전체에 개체를 분산시키기 때문에 종종 유해한 것으로 간주된다. 시간이 지남에 따라 클래스가 밀접하게 결합되기 때문에 구현을 변경하는 것이 어려울 수 있다.

생성 디자인 패턴은 클라이언트를 실제 초기화 프로세스에서 완전히 분리하여 이 문제를 해결한다.

* __싱글톤:__ 애플리케이션 전체에 최대 하나의 개체 인스턴스만 존재하도록 한다.

* __팩토리 메소드:__ 생성할 정확한 객체를 지정하지 않고 여러 관련 클래스의 객체를 생성한다.

* __추상 팩토리:__ 관련 종속 개체의 패밀리를 생성한다.

* __빌더:__ 단계별 접근 방식을 사용하여 복잡한 객체를 구성한다.

## 2. 싱글톤 디자인 패턴
싱글톤 디자인 패턴은 자바 가상 머신 전체에 하나의 개체 인스턴스만 존재하도록 하여 특정 클래스의 개체 초기화를 계속 확인하는 것을 목표로 한다.

Singleton 클래스는 또한 액세스 지점에 대한 각 후속 호출이 해당 특정 개체만 반환하도록 개체에 대한 하나의 고유한 전역 액세스 지점을 제공한다.

### 1) 싱글톤 패턴의 예
Singleton 패턴은 GoF에서 도입되었지만 원래 구현은 다중 스레드 시나리오에서 문제가 있는 것으로 알려져 있다.

그래서 여기에서는 정적 내부 클래스를 사용하는 보다 최적의 접근 방식을 따를 것이다.

```java
public class Singleton  {
    private Singleton() {}
    
    private static class SingletonHolder {
        public static final Singleton instance = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.instance;
    }
}
```

여기에서는 Singleton 클래스의 인스턴스를 보유하는 정적 내부 클래스를 만들었다. `getInstance()` 메서드를 호출할 때만 인스턴스를 생성하고 외부 클래스가 로드될 때는 생성하지 않는다.

이것은 동기화가 필요하지 않고 스레드로부터 안전하며 지연 초기화를 시행하고 상용구가 비교적 적기 때문에 Singleton 클래스에 널리 사용되는 접근 방식이다.

또한 생성자에는 private access modifier가 있다. public 생성자는 누구나 접근할 수 있고 새로운 인스턴스 생성을 시작할 수 있다는 것을 의미 하기 때문에 Singleton을 생성하기 위한 요구 사항이다.

이것은 원래 GoF 구현이 아니라는 것이다. 원본 버전을 보려면 Java의 [Singletons](https://www.baeldung.com/java-singleton)에 대한 내용을 참고한다.

### 2) 싱글톤 디자인 패턴을 사용하는 경우

* 생성하는데 비용이 많이 드는 리소스(예: 데이터베이스 연결 개체)

* 성능을 향상시키기 위해 모든 로거를 싱글톤으로 유지하는 것이 좋다.

* 애플리케이션의 구성 설정에 대한 액세스를 제공하는 클래스

* 공유 모드에서 액세스되는 리소스를 포함하는 클래스

## 3. 팩토리 메소드 디자인 패턴
Factory Design Pattern 또는 Factory Method Design Pattern은 Java에서 가장 많이 사용되는 디자인 패턴 중 하나이다.

GoF에 따르면 이 패턴은 "객체 생성을 위한 인터페이스를 정의하지만 인스턴스화할 클래스는 하위 클래스가 결정하도록 합니다. Factory 메서드를 사용하면 클래스가 인스턴스화를 하위 클래스로 연기할 수 있습니다."

이 패턴은 일종의 가상 생성자를 생성하여 클라이언트에서 특정 팩토리 클래스로 클래스를 초기화하는 책임을 위임한다.

이를 달성하기 위해 우리는 실제 구현 세부 사항을 숨기고 객체를 제공하는 팩토리에 의존한다. 생성된 개체는 공통 인터페이스를 사용하여 액세스된다.

### 1) 팩토리 메소드 디자인 패턴 예제
예제에서 여러 구체적인 클래스에 의해 구현될 Polygon 인터페이스를 만든다. PolygonFactory는 이 패밀리에서 객체를 가져오는데 사용된다.

![factory-method](/assets/img/2022-08-23-creational-design-patterns/factory-method.png)

먼저 Polygon 인터페이스를 생성한다.

```java
public interface Polygon {
    String getType();
}
```

다음으로 이 인터페이스를 구현하고 Polygon 유형의 객체를 반환하는 Square, Triangle 등과 같은 몇 가지 구현을 만든다.

이제 측면의 수를 인수로 사용하고 이 인터페이스의 적절한 구현을 반환하는 공장을 만들 수 있다.

```java
public class PolygonFactory {
    public Polygon getPolygon(int numberOfSides) {
        if(numberOfSides == 3) {
            return new Triangle();
        }
        if(numberOfSides == 4) {
            return new Square();
        }
        if(numberOfSides == 5) {
            return new Pentagon();
        }
        if(numberOfSides == 7) {
            return new Heptagon();
        }
        else if(numberOfSides == 8) {
            return new Octagon();
        }
        return null;
    }
}
```

클라이언트가 객체를 직접 초기화하지 않고도 적절한 Polygon을 제공하기 위해 이 팩토리에 어떻게 의존할 수 있는지 확인한다.

### 2) 팩토리 메소드 디자인 패턴을 사용하는 경우

* 인터페이스 또는 추상 클래스의 구현이 자주 변경될 것으로 예상되는 경우

* 현재 구현이 새로운 변화를 편안하게 수용할 수 없을 때

* 초기화 프로세스가 비교적 간단하고 생성자가 소수의 매개변수만 필요로 하는 경우

## 4. 추상적인 팩토리 디자인 패턴
Abstract Factory가 "구체적인 클래스를 지정하지 않고 관련 또는 종속 개체 패밀리를 생성하기 위한 인터페이스를 제공한다"고 말한다. 즉, 이 모델을 사용하면 일반적인 패턴을 따르는 개체를 만들 수 있다.

JDK의 추상 팩토리 디자인 패턴의 예는 `javax.xml.parsers.DocumentBuilderFactory` 클래스의 newInstance() 이다.

### 1) 추상 팩토리 디자인 패턴 예제
예에서 Factory Method Design 패턴의 두 가지 구현인 AnimalFactory와 Color Factory를 만든다.

그런 다음 Abstract Factory AbstractFactory를 사용하여 액세스를 관리한다.

![abstract-factory](/assets/img/2022-08-23-creational-design-patterns/abstract-factory.png)

먼저 Animal 클래스 패밀리를 만들고 나중에 Abstract Factory에서 사용할 것이다.

다음은 Animal 인터페이스이다.

```java
public interface Animal {
    String getAnimal();
    String makeSound();
}
```

구체적인 구현

```java
public class Duck implements Animal {

    @Override
    public String getAnimal() {
        return "Duck";
    }

    @Override
    public String makeSound() {
        return "Squeks";
    }
}
```

게다가, 정확히 이 방식으로 Animal 인터페이스(Dog, Bear 등)의 보다 구체적인 구현을 생성할 수 있다.

추상 팩토리는 종속 객체 패밀리를 다룬다. 이를 염두에 두고 몇 가지 구현(흰색, 갈색 등)이 있는 인터페이스로 Color 계열을 하나 더 살펴본다.

이제 여러 패밀리가 준비되었으므로 이를 위한 AbstractFactory 인터페이스를 만들 수 있다.

```java
public interface AbstractFactory<T> {
    T create(String animalType) ;
}
```

다음으로 Factory Method 디자인 패턴을 사용하여 AnimalFactory를 구현한다.

```java
public class AnimalFactory implements AbstractFactory<Animal> {

    @Override
    public Animal create(String animalType) {
        if ("Dog".equalsIgnoreCase(animalType)) {
            return new Dog();
        } else if ("Duck".equalsIgnoreCase(animalType)) {
            return new Duck();
        }

        return null;
    }
}
```

마찬가지로 동일한 디자인 패턴을 사용하여 Color 인터페이스에 대한 팩토리를 구현할 수 있다.

이 모든 것이 설정되면 getFactory() 메서드에 제공하는 인수에 따라 AnimalFactory 또는 ColorFactory 구현을 제공하는 FactoryProvider 클래스를 생성한다.

```java
public class FactoryProvider {
    public static AbstractFactory getFactory(String choice){
        
        if("Animal".equalsIgnoreCase(choice)){
            return new AnimalFactory();
        }
        else if("Color".equalsIgnoreCase(choice)){
            return new ColorFactory();
        }
        
        return null;
    }
}
```

### 2) 추상 팩토리 패턴을 사용하는 경우

* 클라이언트는 시스템에서 객체를 생성하고 구성하는 방식과 무관하다.

* 시스템은 여러 객체 패밀리로 구성되며 이러한 패밀리는 함께 사용하도록 설계되었다.

* 특정 종속성을 구성하려면 런타임 값이 필요하다.

패턴은 미리 정의된 개체를 만들 때 훌륭하지만 새 개체를 추가하는 것은 어려울 수 있다. 새로운 유형의 객체를 지원하려면 AbstractFactory 클래스와 모든 하위 클래스를 변경해야 힌다.

## 5. 빌더 디자인 패턴
빌더 디자인 패턴은 비교적 복잡한 객체의 구성을 처리하도록 설계된 또 다른 생성 패턴이다.

객체 생성의 복잡성이 증가하면 빌더 패턴은 객체를 구성하기 위해 다른 객체(빌더)를 사용하여 인스턴스화 프로세스를 분리할 수 있다.

그런 다음 이 빌더를 사용하여 간단한 단계별 접근 방식을 사용하여 다른 많은 유사한 표현을 생성할 수 있다.

### 1) 빌더 패턴 예제
GoF에서 도입한 오리지널 Builder Design Pattern은 추상화에 중점을 두어 복잡한 객체를 다룰때 매우 좋은데 디자인이 조금 복잡하다.

Joshua Bloch는 그의 저서 Effective Java에서 깨끗하고 가독성이 높으며(유창한 디자인을 사용하기 때문에) 클라이언트의 관점에서 사용하기 쉬운 빌더 패턴의 개선된 버전을 소개했다. 이 예에서는 해당 버전에 대해 설명한다.

이 예제에는 빌더를 정적 내부 클래스로 포함하는 BankAccount라는 하나의 클래스만 있다.

```java
public class BankAccount {
    
    private String name;
    private String accountNumber;
    private String email;
    private boolean newsletter;

    // constructors/getters
    
    public static class BankAccountBuilder {
        // builder code
    }
}
```

필드의 모든 액세스 수정자는 외부 개체가 직접 액세스하는 것을 원하지 않으므로 private로 선언 된다.

생성자는 또한 private 이므로 이 클래스에 할당된 Builder만 액세스할 수 있다. 생성자에 설정된 모든 속성은 우리가 인수로 제공하는 빌더 객체에서 추출된다.

정적 내부 클래스에 BankAccountBuilder를 정의 했다.

```java
public static class BankAccountBuilder {
    
    private String name;
    private String accountNumber;
    private String email;
    private boolean newsletter;
    
    public BankAccountBuilder(String name, String accountNumber) {
        this.name = name;
        this.accountNumber = accountNumber;
    }

    public BankAccountBuilder withEmail(String email) {
        this.email = email;
        return this;
    }

    public BankAccountBuilder wantNewsletter(boolean newsletter) {
        this.newsletter = newsletter;
        return this;
    }
    
    public BankAccount build() {
        return new BankAccount(this);
    }
}
```

외부 클래스에 포함된 동일한 필드 세트를 선언했음을 주의한다. 모든 필수 필드는 내부 클래스의 생성자에 대한 인수로 필요하지만 나머지 선택적 필드는 setter 메서드를 사용하여 지정할 수 있다.

이 구현은 또한 setter 메서드가 빌더 개체를 반환하도록 하여 유창한 디자인 접근 방식을 지원한다.

마지막으로 build 메서드는 외부 클래스의 private 생성자를 호출하고 자신을 인수로 전달한다. 반환된 BankAccount는 BankAccountBuilder에 의해 설정된 매개변수로 인스턴스화된다.

작동 중인 빌더 패턴의 간단한 예이다.

```java
BankAccount newAccount = new BankAccount
  .BankAccountBuilder("Jon", "22738022275")
  .withEmail("jon@example.com")
  .wantNewsletter(true)
  .build();
```

### 2) 빌더 패턴을 사용하는 경우

* 객체 생성과 관련된 프로세스가 매우 복잡하고 필수 및 선택적 매개변수가 많은 경우

* 생성자 매개변수의 수가 증가하면 생성자 목록이 많아지는 경우

* 클라이언트가 생성된 객체에 대해 다른 표현을 기대하는 경우

## [출처 및 참고]
* <https://www.baeldung.com/creational-design-patterns>
* <https://www.baeldung.com/java-abstract-factory-pattern>
