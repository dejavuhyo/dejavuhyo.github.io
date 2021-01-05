--- 
title: Prototype Pattern
author: Hyosik
date: 2021-01-05 09:30:00 +0900
categories: [Language, Java]
tags: [prototype-pattern, prototype, design-pattern, 프로토타입-패턴, 디자인-패턴]
---

## 1. Prototype 패턴이란
인스턴스를 작성할 때는 new라는 Java 언어의 키워드를 사용해서 클래스 이름을 지정해서 인스턴스를 생성한다. 이와 같이 new로 인스턴스를 만들 경우에는 클래스 이름을 반드시 지정해야 한다. 그러나 클래스 이름을 지정하지 않고 인스턴스를 생성할 때도 있다. 다음과 같은 경우에는 클래스로부터 인스턴스를 만드는 것이 아니라 인스턴스를 복사해서 새로운 인스턴스를 만든다.

* 종류가 너무 많아 클래스로 정리되지 않는 경우
  - 취급하는 오브젝트의 종류가 너무 많아서 각각을 별도의 클래스로 만들어 다수의 소스 파일을 작성해야 하는 경우이다.

* 클래스로부터 인스턴스 생성이 어려운 경우
  - 생성하고 싶은 인스턴스가 복잡한 작업을 거쳐 만들어지기 때문에 클래스로부터 만들기가 매우 어려운 경우이다.

* framework와 생성할 인스턴스를 분리하고 싶은 경우
  - 인스턴스를 생헝할 때의 framework를 특정 클래스에 의존하지 않도록 만들고 싶은 경우이다. 이와 같은 경우에는 클래스 이름을 지정해서 인스턴스를 만드는 것이 아니라 이미 '모형'이 되는 인스턴스를 등록해 두고, 그 등록된 인스턴스를 복사해서 인스턴스를 생성한다.

클래스로부터 인스턴스를 생성하는 것이 아니라 인스턴스로부터 별도의 인스턴스를 만드는 것을 _**Prototype 패턴**_ 이라고 한다. Prototype은 '원형'이나 '모범'이라는 의미이다. 원형이 되는 인스턴스, 모범이 되는 인스턴스를 기초로 새로운 인스턴스를 만드는 것이다.

## 2. 객체

* Prototype 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-05-prototype-pattern/img001.png)

### 1) Prototype(원형)의 역할
Prototype은 인스턴스를 복사하여 새로운 인스턴스를 만들기 위한 메소드를 결정한다.

### 2) ConcretePrototype(구체적인 원형)의 역할
ConcretePrototype은 인스턴스를 복사해서 새로운 클래스를 만드는 메소드를 실제로 구현한다.

### 3) Client(이용자)의 역할
Client는 인스턴스를 복사하는 메소드를 이용해서 새로운 인스턴스를 만든다.

## 3. 예제

* Product 인터페이스
  - java.lang.Cloneable 인터페이스를 상속하고 있으며, 복제를 가능하게 한다. 이 인터페이스를 구현하고 있는 클래스의 인스턴스는 clone 메소드를 사용해서 자동적으로 복제를 할 수 있다.

```java
package framework;
import java.lang.Cloneable;

public interface Product extends Cloneable {
    public abstract void use(String s);
    public abstract Product createClone();
}
```

* Manager 클래스
  - Product 인터페이스를 이용해서 인스턴스의 복제를 실행하는 클래스이다. showcase 필드는 인스턴스의 '이름'과 '인스턴스'의 대응관계를 java.util.HashMap으로 표현하였다.
  - Product 인터페이스나 Manager 클래스의 소스에 MessageBox 클래스나 UnderlinePen 클래스의 클래스 이름이 전혀 나오지 않는 점에 주의해야한다. 클래스 이름이 나오지 않는다는 것은 Product와 Manager는 그들의 클래스와는 독립적으로 수정할 수 있다는 것을 의미한다. 소스 안에 클래스의 이름을 쓰면 그 클래스와 밀접한 관계가 생긴다.
  - Manager 클래스에서는 구체적인 개개의 클래스 이름을 쓰지 않고 단지 Product라는 인터페이스 이름만 사용하고 있다. 이 인터페이스만이 Manager 클래스와 다른 클래스의 다리 역할을 한다.

```java
package framework;
import java.util.*;

public class Manager {
    private HashMap showcase = new HashMap();
    public void register(String name, Product proto) {
        showcase.put(name, proto);
    }
    public Product create(String protoname) {
        Product p = (Product)showcase.get(protoname);
        return p.createClone();
    }
}
```

* MessageBox 클래스
  - Product 인터페이스를 구현(implements)하고 있다. createClone 메소드는 자기 자신을 복제하는 메소드이다. 복제를 생성할 때 인스턴스가 가지고 있는 필드의 값도 그대로 새로운 인스턴스에 복사된다.
  - clone 메소드로 복사 가능한 것은 java.lang.Cloneable 인터페이스를 구현하고 있는 클래스뿐이다. 이 인터페이스가 구현되고 있지 않은 경우에는 예외 CloneNotSupportedException이 발생한다.
  - Java의 clone 메소드는 자신의 클래스(혹은 하위 클래스)에서만 호출할 수 있기 때문에 다른 클래스의 요청으로 복제를 하는 경우에는 createClone과 같은 메소드를 이용해서 clone을 기술할 필요가 있다.

```java
import framework.*;

public class MessageBox implements Product {
    private char decochar;
    public MessageBox(char decochar) {
        this.decochar = decochar;
    }
    public void use(String s) {
        int length = s.getBytes().length;
        for (int i = 0; i < length + 4; i++) {
            System.out.print(decochar);
        }
        System.out.println("");
        System.out.println(decochar + " "  + s + " " + decochar);
        for (int i = 0; i < length + 4; i++) {
            System.out.print(decochar);
        }
        System.out.println("");
    }
    public Product createClone() {
        Product p = null;
        try {
            p = (Product)clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return p;
    }
}
```

* UnderlinePen 클래스
  - MessageBox와 거의 같은 동작을 하지만, ulchar라는 필드가 밑줄로 이용된다.

```java
import framework.*;

public class UnderlinePen implements Product {
    private char ulchar;
    public UnderlinePen(char ulchar) {
        this.ulchar = ulchar;
    }
    public void use(String s) {
        int length = s.getBytes().length;
        System.out.println("\""  + s + "\"");
        System.out.print(" ");
        for (int i = 0; i < length; i++) {
            System.out.print(ulchar);
        }
        System.out.println("");
    }
    public Product createClone() {
        Product p = null;
        try {
            p = (Product)clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return p;
    }
}
```

* Main 클래스
  - Manager의 인스턴스를 만든다. 그리고 Manager의 인스턴스에 대해서 UnderlinePen의 인스턴스와 MessageBox의 인스턴스에 이름을 붙여서 등록한다.

```java
import framework.*;

public class Main {
    public static void main(String[] args) {
        // 준비
        Manager manager = new Manager();
        UnderlinePen upen = new UnderlinePen('~');
        MessageBox mbox = new MessageBox('*');
        MessageBox sbox = new MessageBox('/');
        manager.register("strong message", upen);
        manager.register("warning box", mbox);
        manager.register("slash box", sbox);

        // 생성
        Product p1 = manager.create("strong message");
        p1.use("Hello, world.");
        Product p2 = manager.create("warning box");
        p2.use("Hello, world.");
        Product p3 = manager.create("slash box");
        p3.use("Hello, world.");
    }
}
```

## 4. 클래스에서 인스턴스를 만들면 안되는 이유

* 종류가 너무 많아서 클래스로 정리할 수 없는 경우

* 클래스로부터 인스턴스 생성이 어려운 경우

* frakework와 생성하는 인스턴스를 분리하고 싶은 경우

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://commons.wikimedia.org/wiki/Prototype_(design_pattern)>
