---
title: Java final 키워드
author: dejavuhyo
date: 2022-08-24 10:30:00 +0900
categories: [Language, Java]
tags: [java-final, final, final-classes, final-methods, final-variables, 자바-final, final-키워드]
---

## 1. final
상속을 통해 기존 코드를 재사용할 수 있지만 때때로 다양한 이유로 확장성에 대한 제한을 설정 해야한다. final 키워드를 사용하면 가능하다.

## 2. Final Classes
final로 표시된 수업은 연장할 수 없다. Java 핵심 라이브러리의 코드를 보면 거기에서 많은 final 클래스를 찾을 수 있다. 한 가지 예는 String 클래스이다.

String 클래스를 확장하고 해당 메서드를 재정의하고 모든 String 인스턴스를 특정 String 하위 클래스의 인스턴스로 대체 할 수 있는 상황을 고려한다.

그러면 String 개체에 대한 작업 결과를 예측할 수 없게 된다. 그리고 String 클래스가 모든 곳에서 사용된다는 점을 감안하면 받아들일 수 없다. 이것이 String 클래스가 final로 표시되는 이유 이다.

final 클래스에서 상속을 시도 하면 컴파일러 오류가 발생한다. 이를 보여주기 위해 final 클래스인 Cat을 생성한다.

```java
public final class Cat {

    private int weight;
    
    // standard getter and setter
}
```

그리고 그것을 확장한다.

```java
public class BlackCat extends Cat {
}
```

컴파일러 오류가 표시된다.

```text
The type BlackCat cannot subclass the final class Cat
```

클래스 선언의 final 키워드는 이 클래스의 객체가 변경 불가능하다는 것을 의미하지 않는다. Cat 객체의 필드를 자유롭게 변경할 수 있다.

```java
Cat cat = new Cat();
cat.setWeight(1);

assertEquals(1, cat.getWeight());
```

그것을 연장할 수 없다.

좋은 디자인의 규칙을 엄격하게 따른다면 클래스를 신중하게 만들고 문서화하거나 안전상의 이유로 final로 선언해야 한다. 그러나 final 클래스를 생성할 때는 주의해야 한다.

클래스를 final로 만드는 것은 다른 프로그래머가 클래스를 개선할 수 없다는 것을 의미한다. 클래스를 사용하고 있고 이에 대한 소스 코드가 없고 한 메서드에 문제가 있다고 상상해본다.

클래스가 final이면 메서드를 재정의하고 문제를 해결하기 위해 클래스를 확장할 수 없다. 즉, 객체지향 프로그래밍의 장점 중 하나인 확장성을 잃게 된다.

## 3. Final Methods
final로 표시된 메서드는 재정의할 수 없다. 클래스를 디자인하고 메서드를 재정의해서는 안 된다고 생각하면 이 메서드를 final로 만들 수 있다. 또한 Java 핵심 라이브러리에서 많은 final 메서드를 찾을 수 있다.

때로는 클래스 확장을 완전히 금지할 필요는 없지만 일부 메서드의 재정의만 방지할 수 있다. 이것의 좋은 예는 Thread 클래스이다. 이를 확장하여 사용자 지정 스레드 클래스를 만드는 것은 합법적이다. 그러나 `isAlive()` 메서드는 final이다.

이 메서드는 스레드가 활성 상태인지 확인한다. 여러 가지 이유로 `isAlive()` 메서드를 올바르게 재정의하는 것은 불가능하다. 그 중 하나는 이 방법이 기본이라는 것이다. 네이티브 코드는 다른 프로그래밍 언어로 구현되며 종종 실행되는 운영 체제 및 하드웨어에 따라 다르다.

Dog 클래스를 만들고 `sound()` 메서드를 final로 지정한다.

```java
public class Dog {
    public final void sound() {
        // ...
    }
}
```

이제 Dog 클래스를 확장하고 `sound()` 메서드를 재정의한다.

```java
public class BlackDog extends Dog {
    public void sound() {
    }
}
```

컴파일러 오류가 표시된다.

```text
- overrides
com.baeldung.finalkeyword.Dog.sound
- Cannot override the final method from Dog
sound() method is final and can’t be overridden
```

클래스의 일부 메소드가 다른 메소드에 의해 호출되면 호출된 메소드를 final로 만드는 것을 고려해야 한다. 그렇지 않으면 재정의하면 호출자의 작업에 영향을 미치고 놀라운 결과를 초래할 수 있다.

생성자가 다른 메서드를 호출하는 경우 일반적으로 위의 이유로 이러한 메서드를 final로 선언해야 한다.

클래스의 모든 메소드를 final로 만드는 것과 클래스 자체를 final로 표시하는 것의 차이점은 첫 번째 경우에는 클래스를 확장하고 새 메서드를 추가할 수 있다. 두 번째 경우에는 이 작업을 수행할 수 없다.

## 4. Final Variables
final로 표시된 변수는 재할당할 수 없다. final 변수가 초기화 되면 변경할 수 없다.

### 1) Final Primitive Variables
원시 final 변수 i를 선언하고 1을 할당한.

그리고 2의 값을 할당해 본다.

```java
public void whenFinalVariableAssign_thenOnlyOnce() {
    final int i = 1;
    //...
    i=2;
}
```

컴파일러는 다음과 같다.

```text
The final local variable i may already have been assigned
```

### 2) Final Reference Variables
final 참조 변수가 있는 경우에도 재할당할 수 없다. 그러나 이것이 참조하는 객체가 변경할 수 없다는 것을 의미하지는 않는다. 이 객체의 속성을 자유롭게 변경할 수 있다.

이를 증명하기 위해 final 참조 변수 cat을 선언 하고 초기화 해본다.

```java
final Cat cat = new Cat();
```

재할당하려고 하면 컴파일러 오류가 표시된다.

```text
The final local variable cat cannot be assigned. It must be blank and not using a compound assignment
```

그러나 Cat 인스턴스의 속성을 변경할 수 있다.

```java
cat.setWeight(5);

assertEquals(5, cat.getWeight());
```

### 3) Final Fields
final 필드는 상수 또는 1회 쓰기 필드일 수 있다. 그것들을 구별하기 위해 객체를 직렬화한다면, 객체의 일부가 아니라 상수이다.

명명 규칙에 따라 클래스 상수는 대문자여야 하며 구성 요소는 밑줄(`_`) 문자로 구분되어야 한다.

```java
static final int MAX_WIDTH = 999;
```

모든 final 필드는 생성자가 완료되기 전에 초기화되어야 한다.

정적 final 필드의 경우 이는 초기화할 수 있음을 의미한다.

* 위의 예와 같이 선언 시

* 정적 초기화 블록에서

예를 들어 final 필드의 경우 초기화할 수 있음을 의미한다.

* 선언 시

* 인스턴스 이니셜라이저 블록에서

* 생성자에서

그렇지 않으면 컴파일러에서 오류가 발생한다.

### 4) Final Arguments
final 키워드는 메소드 인수 앞에 놓는 것도 유효하다. 메서드 내에서 final 인수를 변경할 수 없다.

```java
public void methodWithFinalArguments(final int x) {
    x=1;
}
```

위의 할당으로 인해 컴파일러 오류가 발생한다.

```text
The final local variable x cannot be assigned. It must be blank and not using a compound assignment
```

## [출처 및 참고]
* <https://www.baeldung.com/java-final>
