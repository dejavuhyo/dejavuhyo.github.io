---
title: Java Static 키워드
author: dejavuhyo
date: 2021-12-15 11:14:00 +0900
categories: [Language, Java]
tags: [java-static, java-static-keyword, static-keyword, 자바-static, 자바-static-키워드, static-키워드]
---

## 1. static 키워드 구조
Java 프로그래밍 언어에서 키워드 static은 특정 멤버가 해당 유형의 인스턴스가 아니라 유형 자체에 속한다는 것을 의미한다.

즉, 클래스의 모든 인스턴스에서 공유되는 해당 정적 멤버의 인스턴스를 하나만 생성한다.

![static-variables](/assets/img/2021-12-15-java-static/static-variables.png)

변수, 메서드, 블록 및 중첩 클래스에 키워드를 적용할 수 있다.

## 2. static 필드(또는 클래스 변수)
Java에서 static 필드를 선언하면 해당 필드의 정확히 단일 복사본이 생성되고 해당 클래스의 모든 인스턴스에서 공유된다.

클래스를 초기화하는 횟수는 중요하지 않다. 그것에 속하는 정적 필드의 복사본은 항상 하나만 있다. 이 정적 필드의 값은 동일하거나 다른 클래스의 모든 개체에서 공유된다.

메모리 관점에서 정적 변수는 힙 메모리에 저장된다.

### 1) static 필드의 예
여러 속성(인스턴스 변수)이 있는 Car 클래스가 있다고 가정한다.

이 자동차 청사진에서 새 객체를 초기화할 때마다 각 새 객체에는 이러한 인스턴스 변수의 고유한 복사본이 있다.

그러나 초기화된 Car 개체의 수를 보유하고 모든 인스턴스에서 공유되어 해당 개체에 액세스하고 초기화 시 증가할 수 있는 변수가 필요하다고 가정 한다.

그것이 정적 변수가 들어오는 곳 이다.

```java
public class Car {
    private String name;
    private String engine;
    
    public static int numberOfCars;
    
    public Car(String name, String engine) {
        this.name = name;
        this.engine = engine;
        numberOfCars++;
    }

    // getters and setters
}
```

이제 초기화하는 이 클래스의 모든 개체에 대해 numberOfCars 변수의 동일한 복사본이 증가한다.

따라서 이 경우 다음이 사실이 된다.

```java
@Test
public void whenNumberOfCarObjectsInitialized_thenStaticCounterIncreases() {
    new Car("Jaguar", "V8");
    new Car("Bugatti", "W16");
 
    assertEquals(2, Car.numberOfCars);
}
```

### 2) static 필드를 사용해야 하는 이유

* 변수의 값이 객체와 독립적인 경우

* 값이 모든 개체에서 공유되어야 하는 경우

### 3) 핵심 사항

* 이후 정적 변수는 클래스에 속하는, 우리는 직접 클래스 이름을 사용하여 액세스 할 수 있다. 따라서 객체 참조가 필요하지 않다.

* 클래스 수준에서만 정적 변수를 선언할 수 있다.

* 객체 초기화 없이 정적 필드에 액세스할 수 있다.

* 객체 참조(예: ford.numberOfCars++)를 사용하여 정적 필드에 액세스할 수 있다. 그러나 이것이 인스턴스 변수인지 클래스 변수인지 파악하기 어려워지기 때문에 이것을 피해야 한다. 대신 클래스 이름(Car.numberOfCars++)을 사용하여 항상 정적 변수를 참조해야 한다.

## 3. static 메서드(또는 클래스 메서드)
정적 필드와 마찬가지로 정적 메서드도 개체 대신 클래스에 속한다. 따라서 그들이 상주하는 클래스의 객체를 생성하지 않고 호출할 수 있다.

### 1) 정적 메서드의 예
일반적으로 인스턴스 생성에 의존하지 않는 작업을 수행하기 위해 정적 메서드를 사용한다.

해당 클래스의 모든 인스턴스에서 코드를 공유하기 위해 정적 메서드에 해당 코드를 작성한다.

```java
public static void setNumberOfCars(int numberOfCars) {
    Car.numberOfCars = numberOfCars;
}
```

또한 일반적으로 정적 메서드를 사용하여 유틸리티 또는 도우미 클래스를 만들어 이러한 클래스의 새 개체를 만들지 않고도 가져올 수 있다.

JDK의 Collections 또는 Math 유틸리티 클래스, Apache의 StringUtils 또는 Spring 프레임워크의 CollectionUtils를 살펴보고 모든 메소드가 static 임을 주목한다.

### 2) 정적 메서드를 사용해야 하는 이유

* 객체에 의존하지 않는 정적 변수 및 기타 정적 메서드에 액세스/조작

* 정적 메서드는 utility 및 helper 클래스에서 널리 사용된다.

### 3) 핵심 사항

* Java의 정적 메소드는 컴파일 시간에 해결된다. 메서드 재정의는 런타임 다형성의 일부이므로 정적 메서드는 재정의할 수 없다.

* 추상 메서드는 정적일 수 없다.

* 정적 메서드는 this 또는 super 키워드를 사용할 수 없다.

* 인스턴스, 클래스 메서드 및 변수의 다음 조합이 유효하다.
  - 인스턴스 메서드는 인스턴스 메서드와 인스턴스 변수 모두에 직접 액세스할 수 있다.
  - 인스턴스 메서드는 정적 변수와 정적 메서드에 직접 액세스할 수도 있다.
  - 정적 메서드는 모든 정적 변수 및 기타 정적 메서드에 액세스할 수 있다.
  - 정적 메서드는 인스턴스 변수와 인스턴스 메서드에 직접 액세스할 수 없다. 그렇게 하려면 일부 개체 참조가 필요하다.

## 4. static 블록
정적 변수를 초기화 하기 위해 정적 블록을 사용 한다. 선언하는 동안 정적 변수를 직접 초기화할 수 있지만 여러 줄 처리를 수행해야 하는 상황이 있다.

이러한 경우 정적 블록이 유용하다.

초기화 중에 정적 변수가 추가적인 다중 문 로직을 필요로 하는 경우 정적 블록을 사용할 수 있다.

### 1) static 블록 예
미리 정의된 값으로 목록 개체를 초기화하려고 한다고 가정한다.

이것은 정적 블록을 사용 하면 쉬워진다.

```java
public class StaticBlockDemo {
    public static List<String> ranks = new LinkedList<>();

    static {
        ranks.add("Lieutenant");
        ranks.add("Captain");
        ranks.add("Major");
    }
    
    static {
        ranks.add("Colonel");
        ranks.add("General");
    }
}
```

선언과 함께 모든 초기값으로 List 개체를 초기화하는 것은 불가능하다. 이것이 우리가 여기서 정적 블록을 활용한 이유 이다.

### 2) static 블록을 사용해야 하는 이유

* 정적 변수의 초기화에 할당을 제외한 몇 가지 추가 논리가 필요한 경우

* 정적 변수의 초기화가 오류가 발생하기 쉽고 예외 처리가 필요한 경우

### 3) 핵심 사항

* 클래스에는 여러 개의 정적 블록이 있을 수 있다.

* 정적 필드 및 정적 블록은 클래스에 있는 것과 동일한 순서로 확인 및 실행된다.

## 5. static 클래스
Java 프로그래밍 언어를 사용하면 클래스 내에서 클래스를 생성할 수 있다. 한 곳에서만 사용할 요소를 그룹화하는 강력한 방법을 제공한다. 이렇게 하면 코드를 보다 체계적이고 읽기 쉽게 유지하는데 도움이 된다.

중첩 클래스 아키텍처는 두 가지로 나뉜다.

* 우리가 정적으로 선언한 중첩 클래스를 정적 중첩 클래스라고 한다.

* 정적이 아닌 중첩 클래스를 내부 클래스라고 한다.

이 둘의 주요 차이점은 내부 클래스는 바깥쪽 클래스의 모든 멤버(프라이빗 포함)에 액세스할 수 있는 반면 정적 중첩 클래스는 외부 클래스의 정적 멤버에만 액세스할 수 있다는 것이다.

사실, 정적 중첩 클래스는 다른 최상위 클래스와 똑같이 동작하지만 더 나은 패키징 편의성을 제공하기 위해 액세스할 수 있는 유일한 클래스로 둘러싸여 있다.

### 1) static 클래스의 예
싱글톤 객체를 생성하기 위해 가장 널리 사용되는 접근 방식은 정적 중첩 클래스를 사용하는 것이다.

```java
public class Singleton {
    private Singleton() {}

    private static class SingletonHolder {
        public static final Singleton instance = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.instance;
    }
}
```

동기화가 필요하지 않고 배우고 구현하기 쉽기 때문에 이 방법을 사용한다.

### 2) static 내부 클래스를 사용해야 하는 이유

* 한 곳에서만 사용되는 그룹화 클래스는 캡슐화를 증가시킨다.

* 코드를 사용할 유일한 장소에 더 가깝게 코드를 가져온다. 이렇게 하면 가독성이 향상되고 코드를 더 쉽게 유지 관리할 수 있다.

* 중첩 클래스가 둘러싸는 클래스 인스턴스 멤버에 대한 액세스를 필요로 하지 않는 경우 static으로 선언하는 것이 좋다. 이렇게 하면 외부 클래스에 연결되지 않으므로 힙이나 스택 메모리가 필요하지 않으므로 더 최적화된다.

### 3) 핵심 사항

* 정적 중첩 클래스는 바깥쪽 클래스의 인스턴스 멤버에 액세스할 수 없다. 개체의 참조를 통해서만 액세스할 수 있다.

* 정적 중첩 클래스는 비공개 클래스를 포함하여 바깥쪽 클래스의 모든 정적 멤버에 액세스할 수 있다.

* Java 프로그래밍 사양에서는 최상위 클래스를 정적으로 선언할 수 없다. 클래스 내의 클래스(중첩 클래스)만 static으로 만들 수 있다.

## [출처 및 참고]
* <https://www.baeldung.com/java-static>
