---
title: Factory Method Pattern
author: Hyosik
date: 2020-12-31 09:30:00 +0900
categories: [Language, DesignPattern]
tags: [factory-method-pattern, factory-method, design-pattern, 팩토리-메서드-패턴, 디자인-패턴]
---

## 1. Factory Method 패턴이란
Template Method 패턴에서는 상위 클래스에서 처리의 골격을 만들고, 하위 클래스에서 구체적인 처리의 내용을 만들었다. 이 패턴을 인스턴스 생성의 장면에 적용한 것이 _**Factory Method 패턴**_ 이다.

인스턴스를 생성하는 공장을 Template Method 패턴으로 구성한 것이 Factory Method 패턴이 된다. Factory Method 패턴에서는 인스턴스를 만드는 방법을 상위 클래스 측에서 결정하지만 구체적인 클래스 이름까지는 결정하지 않는다. 구체적인 내용은 모두 하위 클래스 측에서 수행한다. 따라서 인스턴스 생성을 위한 골격(framework)과 실제의 인스턴스 생성의 클래스를 분리해서 생각할 수 있다.

## 2. 객체
Factory Method 패턴을 보면 상위 클래스(추상적인 골격, framework) 측에 있는 Creator 역할과 Product 역할의 관계가 하위 클래스(구체적인 내용, idcard) 측에 있는 ConcreateCreator 역할과 ConcreateProduct 역할의 관계와 병행하고 있음을 알 수 있다.

* Factory Method 패턴의 클래스

![img001](/assets/img/2020-12-31-factory-method-pattern/img001.png)

### 1) Product(제품)의 역할
이것은 framework 쪽에 포함되어 있다. 이 패턴에서 생성되는 인스턴스가 가져야 할 인터페이스(API)를 결정하는 것은 추상 클래스이다. 구체적인 내용은 하위 클래스의 ConcreateProduct 역할이 결정한다.

### 2) Creator(작성자)의 역할
Product 역할을 생성하는 추상 클래스는 framework 쪽에 가깝다. 구체적인 내용은 하위 클래스의 ConcreateCreator 역할이 결정한다. Creator 역할은 실제로 생성하는 ConcreateProduct 역할에 가지고 있는 정보가 없다. Creator 역할이 가지고 있는 정보는 Product 역할과 인스턴스 생성의 메소드를 호출하면 Product가 생성된다는 것뿐이다. new를 사용해서 실제의 인스턴스를 생성하는 대신에, 인스턴스 생성을 위한 메소드를 호출해서 구체적인 클래스 이름에 의한 속박에서 상위 클래스를 자유롭게 만든다.

### 3) ConcreateProduct(구체적인 제품)의 역할
구체적인 제품을 결정하며, idcard 쪽에 해당된다.

### 4) ConcreateCreator(구체적인 작성자)의 역할
구체적인 제품을 만드는 클래스를 결정하며, idcard 쪽에 해당된다.

## 3. 예제

### 1) Product 클래스
이 클래스에서는 추상 메소드 use만이 선언되어 있다. 구체적인 use의 구현은 모두 Product의 하위 클래스에게 맡기고 있다.

```java
package framework;

public abstract class Product {
    public abstract void use();
}
```

### 2) Factory 클래스
Template Method 패턴이 사용되고 있다. 추상 메소드 createProduct에서는 '제품을 만들고', 만든 제품을 추상 메소드 registerProduct에서 '등록'한다. '제품을 만들고', '등록'하는 구현은 하위 클래스에서 수행한다.

```java
package framework;

public abstract class Factory {
    public final Product create(String owner) {
        Product p = createProduct(owner);
        registerProduct(p);
        return p;
    }
    protected abstract Product createProduct(String owner);
    protected abstract void registerProduct(Product product);
}
```

### 3) IDCard 클래스

```java
package idcard;
import framework.*;

public class IDCard extends Product {
    private String owner;
    IDCard(String owner) {
        System.out.println(owner + "의 카드를 만듭니다.");
        this.owner = owner;
    }
    public void use() {
        System.out.println(owner + "의 카드를 사용합니다.");
    }
    public String getOwner() {
        return owner;
    }
}
```

### 4) IDCardFactory 클래스
  - createProduct와 registerProduct의 두 가지 메소드를 구현하고 있다. createProduct에서는 IDCard의 인스턴스를 생성해서 '제품을 만드는'일을 실현하고 있다. registerProduct에서는 IDCard의 owner(소유자)를 owners 필드에 추가해서 '등록'이라는 기능을 실현하고 있다.

```java
package idcard;
import framework.*;
import java.util.*;

public class IDCardFactory extends Factory {
    private List owners = new ArrayList();
    protected Product createProduct(String owner) {
        return new IDCard(owner);
    }
    protected void registerProduct(Product product) {
        owners.add(((IDCard)product).getOwner());
    }
    public List getOwners() {
        return owners;
    }
}
```

### 5) Main 클래스

```java
import framework.*;
import idcard.*;

public class Main {
    public static void main(String[] args) {
        Factory factory = new IDCardFactory();
        Product card1 = factory.create("홍길동");
        Product card2 = factory.create("이순신");
        Product card3 = factory.create("강감찬");
        card1.use();
        card2.use();
        card3.use();
    }
}
```

## 4. 개념 확장

### 1) 인스턴스 생성(메소드 구현 방법)

* 추상 메소드로 한다.
  - 추상 메소드로 하면 하위 클래스는 반드시 이 메소드를 구현해야 한다. 구현되어있지 않으면 컴파일할 때 검출된다.

* 디폴트의 구현을 준비해 둔다.
  - 디폴트의 구현을 준비해 두고 하위 클래스에서 구현하지 않았을 때 사용하면 된다.

* 에러를 이용한다.
  - 디폴트의 구현 내용을 에러로 처리해 두면, 하위 클래스에서 구현하지 않았을 경우에는 실행할 때 에러가 발생한다.(에러가 발생해서 구현되고 있지 않은 것을 알려준다.)

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://commons.wikimedia.org/wiki/File:Factory_Method_UML_class_diagram.svg>
