---
title: Java ==와 equals()의 차이점
author: dejavuhyo
date: 2022-10-27 16:00:00 +0900
categories: [Language, Java]
tags: [java-equals, java-equals-method, java-equals-operator, equals-method-operator, equals-method, 참조-평등, 가치-평등, 자바-등식-연산자, 자바-equals-메서드, 등식-연산자, equals-메서드, 등호-연산자]
---

## 1. 참조 평등
등호 연산자(`==`)로 표시되는 참조 비교를 설명한다. 참조 평등은 두 참조가 메모리의 동일한 개체를 가리킬 때 발생한다.

### 1) 기본 유형이 있는 등호 연산자
[Java의 기본 유형](https://www.baeldung.com/java-primitives)이 단순하고 클래스가 아닌 원시 값이다. 같음 연산자를 기본 유형과 함께 사용할 때 값을 비교하는 것뿐이다.

```java
int a = 10;
int b = 15;
assertFalse(a == b);

int c = 10;
assertTrue(a == c);

int d = a;
assertTrue(a == d);
```

위에 표시된 것처럼 같음 및 참조 검사는 primitives에 대해 동일하게 작동한다. 동일한 값으로 새 기본 요소를 초기화하면 검사가 true를 반환한다. 게다가, 새로운 변수에 원점 값을 재할당하고 비교하면 연산자는 동일한 결과를 반환한다.

null 검사를 수행해 본다.

```java
int e = null; // compilation error
assertFalse(a == null); // compilation error
assertFalse(10 == null); // compilation error
```

Java는 기본 요소에 null을 할당하는 것을 금지한다. 일반적으로 기본 변수 또는 값에 대해 등호 연산자를 사용하여 null 검사를 수행할 수 없다.

### 2) 객체 유형이 있는 등식 연산자
[Java의 객체 유형](https://www.baeldung.com/java-classes-objects)에 대해 같음 연산자는 객체 값을 무시하고 참조 같음 비교만 수행한다. 테스트를 구현하기 전에 간단한 사용자 정의 클래스를 만들어 본다.

```java
public class Person {
    private String name;
    private int age;

    // constructor, getters, setters...
}
```

일부 클래스 개체를 초기화하고 항등 연산자 결과를 검사해 본다.

```java
Person a = new Person("Bob", 20);
Person b = new Person("Mike", 40);
assertFalse(a == b);

Person c = new Person("Bob", 20);
assertFalse(a == c);

Person d = a;
assertTrue(a == d);
```

결과는 이전과 상당히 다르다. 두 번째 검사는 기본 요소에 대해 참인 동안 거짓을 반환한다. 앞에서 언급했듯이 동등 연산자는 비교할 때 객체의 내부 값을 무시한다. **두 변수가 동일한 메모리 주소를 참조하고 있는지만 확인**한다.

프리미티브와 달리 객체로 작업하는 동안 null을 사용할 수 있다.

```java
assertFalse(a == null);
Person e = null;
assertTrue(e == null);
```

항등 연산자를 사용하고 null을 비교하여 변수에 할당된 개체가 이미 초기화되었는지 확인한다.

## 2. 가치 평등
값 평등은 두 개의 개별 객체가 동일한 값이나 상태를 가질 때 발생한다.

이것은 값을 비교하며 Object의 `equals()` 메서드와 밀접하게 관련되어 있다. 이전과 마찬가지로 주요 차이점을 살펴보고 프리미티브 및 객체 유형과 사용을 비교한다.

### 1) 기본 유형이 있는 equals() 메서드
프리미티브는 단일 값을 가진 기본 유형이며 어떤 메소드도 구현하지 않는다. 따라서 primitives를 사용하여 `equals()` 메서드를 직접 호출하는 것은 불가능하다.

```java
int a = 10;
assertTrue(a.equals(10)); // compilation error
```

그러나 모든 기본 요소에는 자체 래퍼 클래스가 있으므로 boxing 메커니즘을 사용하여 개체 표현으로 캐스팅할 수 있다. 그런 다음 객체 유형을 사용하는 것처럼 `equals()` 메서드를 쉽게 호출할 수 있다.

```java
int a = 10;
Integer b = a;

assertTrue(b.equals(10));
```

### 2) 객체 유형이 있는 equals() 메서드
Person 클래스에서, `equals()` 메서드가 올바르게 작동하려면 클래스에 포함된 필드를 고려하여 사용자 지정 클래스의 메서드를 재정의해야 한다.

```java
public class Person {
    // other fields and methods omitted

    @Override
    public boolean equals(Object o) {
        if (this == o) 
            return true;
        if (o == null || getClass() != o.getClass()) 
            return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }
}
```

`equals()` 메서드는 주어진 값에 동일한 참조가 있으면 true를 반환하며 참조 연산자가 이를 확인한다. 그렇지 않은 경우 평등 테스트를 시작한다.

또한 두 값 모두에 대해 Class 객체의 동등성을 테스트한다. 다른 경우 false를 반환한다. 그렇지 않으면 평등을 계속 확인한다. 마지막으로 각 속성을 개별적으로 비교한 결합 결과를 반환한다.

이전 테스트를 수정하고 결과를 확인한다.

```java
Person a = new Person("Bob", 20);
Person b = new Person("Mike", 40);
assertFalse(a.equals(b));

Person c = new Person("Bob", 20);
assertTrue(a.equals(c));

Person d = a;
assertTrue(a.equals(d));
```

두 번째 검사는 참조 평등과 반대로 true를 반환한다. 재정의된 `equals()` 메서드는 객체의 내부 값을 비교한다.

`equals()` 메서드를 재정의하지 않으면 부모 클래스 Object의 메서드가 사용된다. `Object.equals()` 메서드는 참조 동등성 검사만 수행하므로 Person 객체를 비교할 때 동작이 예상한 것과 다를 수 있다.

위에서 `hashCode()` 메서드를 표시하지 않았지만 `equals()` 메서드를 재정의할 때마다 이 메서드를 재정의하여 이러한 메서드 간의 일관성을 보장하는 것이 중요하다.

## 3. Null 평등
마지막으로 `equals()` 메서드가 null 값과 어떻게 작동하는지 확인한다.

```java
Person a = new Person("Bob", 20);
Person e = null;
assertFalse(a.equals(e));
assertThrows(NullPointerException.class, () -> e.equals(a));
```

다른 객체에 대해 `equals()` 메서드를 사용하여 확인할 때 해당 변수의 순서에 따라 두 가지 다른 결과를 얻는다. 마지막 문은 null 참조에서 `equals()` 메서드를 호출하기 때문에 예외를 발생한다. 마지막 문을 수정하려면 먼저 등호 연산자 검사를 호출해야 한다.

```java
assertFalse(e != null && e.equals(a));
```

조건의 왼쪽이 false를 반환하여 전체 명령문을 false와 같게 만들어 NullPointerException이 발생하는 것을 방지한다. 따라서 `equals()` 메서드를 호출하는 값 이 null이 아닌지 먼저 확인해야 한다. 그렇지 않으면 성가신 버그가 발생할 수 있다.

또한 Java 7부터 null-safe한 [Objects#equals()](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/util/Objects.html#equals(java.lang.Object,java.lang.Object)) 정적 메서드를 사용하여 동등성 검사를 수행할 수 있다.

이 메서드는 NullPointerException 발생을 방지하기 위해 추가 검사를 수행하고 두 매개 변수가 모두 null이면 true를 반환한다.


## 4. 정리
참조 동등성을 테스트하기 위해 `==` 연산자를 사용한다. 이 연산자는 기본값과 객체에 대해 약간 다르게 작동한다. **같음 연산자를 프리미티브와 함께 사용하면 값을 비교한다. 반면에 for objects와 함께 사용하면 메모리 참조를 확인한다.** null 값과 비교하여 객체가 메모리에서 초기화되었는지 확인하기만 하면 된다.

**Java에서 값 동등성 테스트를 수행하기 위해 Object에서 상속된 `equals()` 메서드를 사용 한다. 프리미티브는 클래스가 아닌 단순한 값이므로 이 메서드는 래핑 없이 호출할 수 없다.**

또한 인스턴스 화 된 객체에 대해서만 `equals()` 메서드를 호출 한다. 그렇지 않으면 예외가 발생한다. 이를 방지하기 위해 null 값이 의심되는 경우 `==` 연산자로 값을 확인해야 한다.

## [출처 및 참고]
* <https://www.baeldung.com/java-equals-method-operator-difference>
