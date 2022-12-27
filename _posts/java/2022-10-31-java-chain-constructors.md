---
title: Java 생성자 연결
author: dejavuhyo
date: 2022-10-31 09:00:00 +0900
categories: [Language, Java]
tags: [java-chain-constructors, chain-constructors, constructors, java-this, java-super, 자바-생성자-연결, 생성자-연결, 생성자]
---

## 1. 생성자 연결
Java에서 [생성자](https://www.baeldung.com/java-constructors)의 연결은, 덜 중복된 코드를 생성하고 더 읽기 쉽게 만드는 편리한 디자인 패턴이다.

## 2. 예제를 사용하여 생성자 정의 연결
**생성자 연결은 생성자 시퀀스를 호출하는 프로세스**이다. 두 가지 방법으로 수행할 수 있다.

* 같은 클래스에서 생성자를 연결하기 위해 `this()` 키워드를 사용

* 부모 클래스에서 생성자를 연결하기 위해 `super()` 키워드를 사용

### 1) 같은 클래스 내에서 생성자 연결
몇 가지 속성을 포함하는 간단한 Person 클래스를 정의한다.

```java
public class Person {
    private final String firstName;
    private final String middleName;
    private final String lastName;
    private final int age;

    //getters, equals and hashcode
}
```

firstName, lastName 및 age는 객체 초기화 중에 항상 설정하려는 속성이다. 그러나 모든 사람에게 중간 이름이 있는 것은 아니다. 따라서 middleName 속성은 선택 사항이다.

이를 염두에 두고 두 개의 생성자를 만들 것이다. 첫 번째 속성은 네 가지 속성을 모두 허용한다.

```java
public Person(String firstName, String middleName, String lastName, int age) {
    this.firstName = firstName;
    this.middleName = middleName;
    this.lastName = lastName;
    this.age = age;
}
```

두 번째 생성자는 세 가지 필수 속성을 허용하고 선택적 필드를 생략한다.

```java
public Person(String firstName, String lastName, int age) {
    this(firstName, null, lastName, age);
}
```

**`this()` 키워드를 사용하고 있다. 항상 생성자의 첫 번째 줄이어야 한다.** 이렇게 하면 연결하려는 생성자가 처음에 호출된다.

클래스에서 생성자의 순서는 관련이 없다. 즉, 두 번째 생성자는 Person 클래스의 아무 곳에나 배치할 수 있으며 여전히 올바르게 작동한다.

### 2) 부모 클래스에서 생성자 연결
Person 클래스에서 상속하는 Customer 클래스를 정의한다.

```java
public class Customer extends Person {
    private final String loyaltyCardId;

   //getters, equals and hashcode
}
```

여기에는 추가 속성이 포함되어 있다. 이제 Person 클래스에서와 유사한 방식으로 두 개의 생성자를 생성한다.

```java
public Customer(String firstName, String lastName, int age, String loyaltyCardId) {
    this(firstName, null, lastName, age, loyaltyCardId);
}

public Customer(String firstName, String middleName, String lastName, int age, String loyaltyCardId) {
    super(firstName, middleName, lastName, age);
    this.loyaltyCardId = loyaltyCardId;
}
```

첫 번째 생성자는 `this()` 키워드를 사용하여 모든 필수 및 선택적 속성을 허용하는 두 번째 생성자에 연결한다. 여기서는 처음으로 `super()` 키워드를 사용한다.

동작은 `this()` 키워드와 매우 유사하다. 유일한 차이점은 **`super()`는 부모 클래스의 해당 생성자에 연결되는 반면 `this()`는 동일한 클래스의 생성자에 연결된다는 것**이다.

이전 키워드와 유사하게 `super()`는 항상 생성자의 첫 번째 줄 이어야 한다. 이것은 부모의 생성자가 처음에 호출된다는 것을 의미한다. 그런 다음 값이 loyaltyCardId 속성에 할당된다.

## 3. 생성자 연결의 장점과 단점
**생성자 연결의 가장 큰 장점은 중복 코드가 적다**는 것이다. 다시 말해서, 우리는 Don't Repeat Yourself(DRY) 원칙을 사용한다. 이는 일반적으로 모든 속성을 허용하는 단일 생성자에서 개체의 초기화를 수행하기 때문이다. 다른 생성자는 수신된 데이터를 전달하고 누락된 속성에 대한 기본값을 추가하기 위한 것이다.

**생성자 연결은 코드를 더 읽기 쉽게 만든다.** 모든 생성자에서 속성 할당을 반복할 필요는 없다. 대신 한 곳에서 이 작업을 수행한다.

다른 한편으로, **생성자 연결을 사용할 때 객체를 생성하는 더 많은 방법을 노출한다.** 이것은 일부 프로젝트에서 중요한 단점이 될 수 있다. 이러한 경우 여러 생성자를 숨기기 위해 팩토리 또는 빌더 패턴을 찾아야 한다.

## [출처 및 참고]
* <https://www.baeldung.com/java-chain-constructors>
