---
title: Java super와 super()
author: dejavuhyo
date: 2022-09-06 09:00:00 +0900
categories: [Language, Java]
tags: [java-super, super, 자바-super, 상속, 자바-상속]
---

## 1. super 키워드
super 키워드는 부모 클래스로부터 상속받은 필드나 메소드를 자식 클래스에서 참조하는데 사용하는 참조 변수이다.

인스턴스 변수의 이름과 지역 변수의 이름이 같을 경우 인스턴스 변수 앞에 this 키워드를 사용하여 구분할 수 있다.

이와 마찬가지로 부모 클래스의 멤버와 자식 클래스의 멤버 이름이 같을 경우 super 키워드를 사용하여 구별할 수 있다.

이렇게 자바에서는 super 참조 변수를 사용하여 부모 클래스의 멤버에 접근할 수 있다.

this와 마찬가지로 super 참조 변수를 사용할 수 있는 대상도 인스턴스 메소드뿐이며, 클래스 메소드에서는 사용할 수 없다.

* 예제

```java
class Parent { int a = 10; }

class Child extends Parent {
    void display() {
        System.out.println(a);
        System.out.println(this.a);
        System.out.println(super.a);
    }
}

public class Inheritance02 {
    public static void main(String[] args) {
        Child ch = new Child();
        ch.display();
    }
}
```

* 실행 결과

```text
10
10
10
```

위의 예제에서 int형 변수 num는 부모 클래스인 Parent 클래스에서만 선언되어 있다.

따라서 지역 변수와 this 참조 변수 그리고 super 참조 변수 모두 같은 값을 출력한다.

* 예제

```java
class Parent {
    int a = 10;
}

class Child extends Parent {
    int a = 20;

    void display() {
        System.out.println(a);
        System.out.println(this.a);
        System.out.println(super.a);
    }
}

public class Inheritance03 {
    public static void main(String[] args) {
        Child ch = new Child();
        ch.display();
    }
}
```

* 실행 결과

```text
20
20
10
```

하지만 위의 예제에서 int형 변수 num는 자식 클래스인 Child 클래스에서도 선언되어 있다.

따라서 지역 변수와 this 참조 변수는 자식 클래스에서 대입된 값을 출력하며, super 참조 변수만이 부모 클래스에서 대입된 값을 출력하게 된다.

## 2. super() 메소드
`this()` 메소드가 같은 클래스의 다른 생성자를 호출할 때 사용된다면, `super()` 메소드는 부모 클래스의 생성자를 호출할 때 사용된다.

자식 클래스의 인스턴스를 생성하면, 해당 인스턴스에는 자식 클래스의 고유 멤버뿐만 아니라 부모 클래스의 모든 멤버까지도 포함되어 있다.

따라서 부모 클래스의 멤버를 초기화하기 위해서는 자식 클래스의 생성자에서 부모 클래스의 생성자까지 호출해야만 한다.

이러한 부모 클래스의 생성자 호출은 모든 클래스의 부모 클래스인 Object 클래스의 생성자까지 계속 거슬러 올라가며 수행된다.

따라서 자바 컴파일러는 부모 클래스의 생성자를 명시적으로 호출하지 않는 모든 자식 클래스의 생성자 첫 줄에 자동으로 다음과 같은 명령문을 추가하여, 부모 클래스의 멤버를 초기화할 수 있도록 해준다.

* 문법

```java
super();
```

하지만 자바 컴파일러는 컴파일 시 클래스에 생성자가 하나도 정의되어 있지 않아야만, 자동으로 기본 생성자를 추가해 준다.

만약 다음 예제처럼 부모 클래스에 매개변수를 가지는 생성자를 하나라도 선언했다면, 부모 클래스에는 기본 생성자가 자동으로 추가되지 않을 것이다.

* 예제

```java
class Parent {
    int a;
    Parent(int n) { a = n; }
}
```

그러나 다음 예제처럼 Parent 클래스를 상속받은 자식 클래스에서 `super()` 메소드를 사용하여 부모 클래스의 기본 생성자를 호출하게 되면, 오류가 발생하게 될 것다.

* 예제

```java
class Parent {
    int a;
    Parent(int n) { a = n; }
}

class Child extends Parent {
    int b;
    Child() {
        super();
        b = 20;
    }
```

왜냐하면 부모 클래스인 Parent 클래스에는 기본 생성자가 추가되어 있지 않기 때문이다.

따라서 매개변수를 가지는 생성자를 선언해야 할 경우에는 되도록이면 다음 예제처럼 기본 생성자까지 명시적으로 선언하는 것이 좋다.

* 예제

```java
class Parent {
    int a;
    Parent() { a = 10; }
    Parent(int n) { a = n; }
}

class Child extends Parent {
    int b;
    Child() {
        super();
        b = 20;
    }
```

다음 예제는 `super()` 메소드가 어떻게 호출되는지를 보여주는 예제이다.

* 예제

```java
class Parent {
    int a;
    Parent() { a = 10; }
    Parent(int n) { a = n; }
}

class Child extends Parent {
    int b;
    Child() {
①      //super(40);
        b = 20;
    }

    void display() {
        System.out.println(a);
        System.out.println(b);
    }
}


public class Inheritance04 {
    public static void main(String[] args) {
        Child ch = new Child();
        ch.display();
    }
}
```

* 실행 결과

```text
10
20
```

위의 예제를 그냥 실행하면, 자바 컴파일러는 주석 처리된 ①번 라인에 자동으로 `super();` 구문을 삽입할 것이다.

따라서 변수 a는 10으로 초기화된다.

하지만 ①번 라인의 주석 처리를 해제하고 실행하면, 부모 클래스인 Parent 클래스는 두 번째 생성자에 의해 초기화될 것이다.

따라서 변수 a는 40으로 초기화된다.

## [출처 및 참고]
* [www.tcpschool.com/java/java_inheritance_super](www.tcpschool.com/java/java_inheritance_super)
