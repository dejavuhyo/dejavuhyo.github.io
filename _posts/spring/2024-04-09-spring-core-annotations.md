---
title: Spring Core Annotations
author: dejavuhyo
date: 2024-04-09 10:56:00 +0900
categories: [Framework, Spring]
tags: [spring-core-annotations, spring-annotations, core-annotations]
---

## 1. DI 관련 주석

### 1) @Autowired
`@Autowired`를 사용하여 Spring이 해결하고 주입할 종속성을 표시 할 수 있다. 생성자, 설정자 또는 필드 주입과 함께 이 주석을 사용할 수 있다.

Constructor injection

```java
class Car {
    Engine engine;

    @Autowired
    Car(Engine engine) {
        this.engine = engine;
    }
}
```

Setter injection

```java
class Car {
    Engine engine;

    @Autowired
    void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```

Field injection

```java
class Car {
    @Autowired
    Engine engine;
}
```

`@Autowired`에는 기본값이 true인 필수 라는 boolean 인수가 있다. 연결하기에 적합한 빈을 찾지 못할 때 Spring의 동작을 조정한다. true 이면 예외가 발생하고, 그렇지 않으면 아무것도 연결되지 않는다.

생성자 주입을 사용하는 경우 모든 생성자 인수가 필수이다.

버전 4.3부터는 최소한 두 개의 생성자를 선언하지 않는 한 명시적으로 `@Autowired`로 생성자에 주석을 달 필요가 없다.

### 2) @Bean
`@Bean`은 Spring 빈을 인스턴스화하는 팩토리 메소드를 표시한다.

```java
@Bean
Engine engine() {
    return new Engine();
}
```

Spring은 반환 유형의 새 인스턴스가 필요할 때 이러한 메서드를 호출한다.

결과 빈은 팩토리 메소드와 동일한 이름을 갖는다. 다르게 이름을 지정하려면 이 주석의 name 또는 value 인수를 사용하여 그렇게 할 수 있다(인수 값은 인수 name의 별칭이다).

```java
@Bean("engine")
Engine getEngine() {
    return new Engine();
}
```

`@Bean`으로 주석이 달린 모든 메소드는 `@Configuration` 클래스에 있어야 한다.

### 3) @Qualifier
모호한 상황에서 사용하려는 빈 id 또는 빈 name을 제공하기 위해 `@Autowired`와 함께 `@Qualifier`를 사용한다.

예를 들어, 다음 두 Bean은 동일한 인터페이스를 구현한다.

```java
class Bike implements Vehicle {}

class Car implements Vehicle {}
```

Spring이 Vehicle 빈을 주입해야 하는 경우에는 여러 개의 일치하는 정의로 끝난다. 이러한 경우 `@Qualifier` 주석을 사용하여 명시적으로 Bean의 이름을 제공할 수 있다.

Constructor injection 사용

```java
@Autowired
Biker(@Qualifier("bike") Vehicle vehicle) {
    this.vehicle = vehicle;
}
```

Setter injection 사용

```java
@Autowired
void setVehicle(@Qualifier("bike") Vehicle vehicle) {
    this.vehicle = vehicle;
}
```

또는

```java
@Autowired
@Qualifier("bike")
void setVehicle(Vehicle vehicle) {
    this.vehicle = vehicle;
}
```

field injection 사용

```java
@Autowired
@Qualifier("bike")
Vehicle vehicle;
```

### 4) @Value
Bean에 속성 값을 주입하기 위해 `@Value`를 사용할 수 있다. 생성자, 설정자 및 필드 주입과 호환된다.

Constructor injection

```java
Engine(@Value("8") int cylinderCount) {
    this.cylinderCount = cylinderCount;
}
```

Setter injection

```java
@Autowired
void setCylinderCount(@Value("8") int cylinderCount) {
    this.cylinderCount = cylinderCount;
}
```

또는

```java
@Value("8")
void setCylinderCount(int cylinderCount) {
    this.cylinderCount = cylinderCount;
}
```

Field injection

```java
@Value("8")
int cylinderCount;
```

물론 정적 값을 주입하는 것은 유용하지 않다. 따라서 `@Value`의 자리 표시자 문자열을 사용하여 외부 소스 (예:`.properties` 또는 `.yaml` 파일)에 정의된 값을 연결할 수 있다.

다음 `.properties` 파일을 가정한다.

```properties
engine.fuelType=petrol
```

다음을 사용하여 `engine.fuelType`의 값을 주입할 수 있다.

```java
@Value("${engine.fuelType}")
String fuelType;
```

SpEL에서도 `@Value`를 사용할 수 있다.

### 5) @DependsOn
이 주석을 사용하여 Spring이 주석이 달린 빈보다 먼저 다른 빈을 초기화 하도록 할 수 있다. 일반적으로 이 동작은 Bean 간의 명시적인 종속성을 기반으로 자동으로 수행된다.

JDBC 드라이버 로딩이나 정적 변수 초기화와 같이 종속성이 암시적일 때만 이 주석이 필요하다.

종속성 Bean의 이름을 지정하는 종속 클래스에서 `@DependsOn`을 사용할 수 있다. 주석의 값 인수에는 종속성 Bean 이름을 포함하는 배열이 필요하다.

```java
@DependsOn("engine")
class Car implements Vehicle {}
```

또는 `@Bean` 주석으로 Bean을 정의하는 경우 팩토리 메소드에 `@DependsOn` 주석을 달아야 한다.

```java
@Bean
@DependsOn("fuel")
Engine engine() {
    return new Engine();
}
```

### 6) @Lazy
빈을 느리게 초기화하고 싶을 때 `@Lazy`를 사용한다. 기본적으로 Spring은 애플리케이션 컨텍스트의 시작/부트스트래핑 시 모든 싱글톤 Bean을 열심히 생성한다.

그러나 애플리케이션 시작 시가 아니라 요청할 때 Bean을 생성해야 하는 경우가 있다.

이 주석은 정확히 어디에 배치하느냐에 따라 다르게 동작한다. 우리는 그것을 넣을 수 있다.

* 메소드 호출을 지연시키기 위한 `@Bean` 주석이 달린 빈 팩토리 메소드 (따라서 빈 생성)

* `@Configuration` 클래스와 포함된 모든 `@Bean` 메소드가 영향을 받음

* `@Configuration` 클래스가 아닌 `@Component` 클래스, 이 Bean은 느리게 초기화됨

* (프록시를 통해) 종속성 자체를 느리게 로드하기 위한 `@Autowired` 생성자, 설정자 또는 필드

이 주석에는 기본값이 true인 value라는 인수가 있다. 기본 동작을 재정의하는 것이 유용하다.

예를 들어 전역 설정이 게으른 경우 Bean을 즉시 로드하도록 표시하거나 `@Lazy`로 표시된 `@Configuration` 클래스에서 특정 `@Bean` 메서드를 즉시 로드하도록 구성한다.

```java
@Configuration
@Lazy
class VehicleFactoryConfig {

    @Bean
    @Lazy(false)
    Engine engine() {
        return new Engine();
    }
}
```

### 7) @Lookup
`@Lookup`으로 주석이 달린 메소드는 호출할 때 메소드의 반환 유형 인스턴스를 반환하도록 Spring에 지시한다.

### 8) @Primary
때로는 동일한 유형의 여러 Bean을 정의해야 하는 경우도 있다. 이러한 경우 Spring은 필요한 빈이 무엇인지 전혀 모르기 때문에 주입이 실패한다.

즉, `@Qualifier`로 모든 연결 지점을 표시 하고 필요한 Bean의 이름을 지정하는 것이다.

그러나 대부분의 경우 특정 Bean이 필요하고 다른 Bean은 거의 필요하지 않다. `@Primary`를 사용하여 이 사례를 단순화 할 수 있다. 가장 자주 사용되는 Bean을 `@Primary`로 표시하면 자격이 없는 주입 지점에서 선택된다.

```java
@Component
@Primary
class Car implements Vehicle {}

@Component
class Bike implements Vehicle {}

@Component
class Driver {
    @Autowired
    Vehicle vehicle;
}

@Component
class Biker {
    @Autowired
    @Qualifier("bike")
    Vehicle vehicle;
}
```

이전 예에서는 자동차가 기본 차량이다. 따라서 Driver 클래스에서 Spring은 Car Bean을 주입한다. 물론 Biker 빈에서는 필드 차량의 값이 자격을 갖추었기 때문에 Bike 객체가 된다.

### 9) @Scope
`@Component` 클래스나 `@Bean` 정의의 범위를 정의하기 위해 `@Scope`를 사용한다. 싱글톤, 프로토타입, 요청, 세션, globalSession 또는 일부 사용자 정의 범위 일 수 있다.

예

```java
@Component
@Scope("prototype")
class Engine {}
```

## 2. 컨텍스트 구성 주석
주석을 사용하여 애플리케이션 컨텍스트를 구성할 수 있다.

### 1) @Profile
Spring이 특정 프로필이 활성화된 경우에만 `@Component` 클래스나 `@Bean` 메서드를 사용 하도록 하려면 `@Profile`로 표시하면 된다. 주석의 값 인수를 사용하여 프로필 이름을 구성할 수 있다.

```java
@Component
@Profile("sportDay")
class Bike implements Vehicle {}
```

### 2) @Import
이 주석을 사용하면 구성 요소 검색 없이 특정 `@Configuration` 클래스를 사용할 수 있다. `@Import`의 값 인수를 사용하여 해당 클래스를 제공할 수 있다.

```java
@Import(VehiclePartSupplier.class)
class VehicleFactoryConfig {}
```

### 3) @ImportResource
이 주석을 사용하여 XML 구성을 가져올 수 있다. 위치 인수 또는 별칭인 값 인수를 사용하여 XML 파일 위치를 지정할 수 있다.

```java
@Configuration
@ImportResource("classpath:/annotations.xml")
class VehicleFactoryConfig {}
```

### 4) @PropertySource
이 주석을 사용하면 애플리케이션 설정에 대한 속성 파일을 정의 할 수 있다.

```java
@Configuration
@PropertySource("classpath:/annotations.properties")
class VehicleFactoryConfig {}
```

`@PropertySource`는 Java 8 반복 주석 기능을 활용한다. 즉, 이 주석으로 클래스를 여러 번 표시할 수 있다.

```java
@Configuration
@PropertySource("classpath:/annotations.properties")
@PropertySource("classpath:/vehicle-factory.properties")
class VehicleFactoryConfig {}
```

### 5) @PropertySources
이 주석을 사용하여 여러 `@PropertySource` 구성을 지정할 수 있다.

```java
@Configuration
@PropertySources({ 
    @PropertySource("classpath:/annotations.properties"),
    @PropertySource("classpath:/vehicle-factory.properties")
})
class VehicleFactoryConfig {}
```

Java 8부터는 위에서 설명한 반복 주석 기능을 사용하여 동일한 결과를 얻을 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-core-annotations](https://www.baeldung.com/spring-core-annotations)
