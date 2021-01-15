---
title: Bridge Pattern
author: Hyosik
date: 2021-01-08 10:30:00 +0900
categories: [Language, Java]
tags: [bridge-pattern, bridge, design-pattern, 브리지-패턴, 디자인-패턴]
---

## 1. Bridge 패턴이란
Bridge 패턴이 다리 역할을 하고 있는 두 곳은 '기능의 클래스 계층'과 '구현의 클래스 계층'이다.

기능의 클래스 계층은 새로운 기능을 추가하고 싶을 경우 클래스 계층 안에서 자신의 목적과 가까운 클래스를 찾아내 그 하위 클래스를 만들어, 목적한 기능을 추가한 새로운 클래스를 만드는 것이다.

구현의 클래스 계층은 새로운 구현을 만들기 위해서는 AbstractClass의 하위 클래스를 만들어 추상 메소드를 구현하는 것이다.

'기능의 클래스 계층'과 '구현의 클래스 계층'을 두 개의 독립된 클래스 계층으로 분리한다. 단순히 분리만 하면 흩어져버리기 때문에 두 개의 클래스 계층 사이에 다리를 놓는 일이 필요하다. 이와 같이 다리놓기를 수행하는 패턴을 _**Bridge 패턴**_ 이라고 한다.

## 2. 객체

* Bridge 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-08-bridge-pattern/img001.png)

### 1) Abstaction(추상화)의 역할
'기능의 클래스 계층'의 최상위 클래스이다. Implementor 역할의 메소드를 사용해서 기본적인 기능만이 기술되어 있는 클래스이다. 이 인스턴스는 Implementor 역할을 가지고 있다.

### 2) RefinedAbstaction(개선된 추상화)의 역할
Abstaction 역할에 대해 기능을 추가한 역할이다.

### 3) Implementor(구현자)의 역할
'구현의 클래스 계층'의 최상위 클래스이다. Abstaction 역할의 인터페이스(API)를 구현하기 위한 메소드를 규정하는 역할이다.

### 4) Concrete Implementor(구체적인 구현자)의 역할
Implementor 역할의 인터페이스(API)를 구체적으로 구현하는 역할이다.

## 3. 예제

### 1) Display 클래스
추상적인 '무언가를 표시하는 것'이다. 이 클래스는 '기능의 클래스 계층'의 최상위에 있는 클래스이다. impl 필드는 Display 클래스의 '구현'을 나타내는 인스턴스이다.

생성자에는 구현을 나타내는 클래스의 인스턴스를 전달한다. 인수로 전달된 인스턴스는 impl 필드에 저장되어 이후에 처리할 때 사용된다.

open, print, close 세 메소드는 Display 클래스가 제공하는 인터페이스(API)에서 표시를 실행하는 순서를 나타내고 있다. open은 표시의 전처리, print는 표시 그 자체, close는 표시의 후처리 역할을 맡는다. 메소드들을 실현하기 위해 impl 필드의 구현 메소드를 이용하고 있다. 여기에서 Display의 인터페이스(API)가 DisplayImpl의 인터페이스(API)로 변환된다. display 메소드는 open, print, close라는 Display의 인터페이스(API)를 이용해서 '표시한다'라는 처리를 실현하고 있다.

```java
public class Display {
    private DisplayImpl impl;
    public Display(DisplayImpl impl) {
        this.impl = impl;
    }
    public void open() {
        impl.rawOpen();
    }
    public void print() {
        impl.rawPrint();
    }
    public void close() {
        impl.rawClose();
    }
    public final void display() {
        open();
        print();
        close();
    }
}
```

### 2) CountDisplay 클래스
Display 클래스에 기능을 추가한 것이 CountDisplay이다. Display 클래스에는 '표시한다'는 기능밖에 없지만 CountDisplay 클래스에서는 '지정횟수만큼 표시한다'는 기능이 추가 되었다. 이것이multiDisplay 메소드이다. CountDisplay 클래스에서는 open, print, close라는 Display 클래스에서 상속받은 메소드를 사용해서 새로운 메소드를 추가하고 있다.

```java
public class CountDisplay extends Display {
    public CountDisplay(DisplayImpl impl) {
        super(impl);
    }
    public void multiDisplay(int times) {       // times회 반복해서 표시하기
        open();
        for (int i = 0; i < times; i++) {
            print();
        }
        close();
    }
}
```

### 3) DisplayImpl 클래스
DisplayImpl 클래스는 '구현의 클래스 계층'의 최상위에 위치한다. DisplayImpl 클래스는 추상 클래스이며 rawOpen, rawPrint, rawClose라는 세 가지 메소드를 가지고 있다. 이것은 Display 클래스의 open, print, close에 각각 대응하며 전처리, 표시, 후처리를 실행한다.

```java
public abstract class DisplayImpl {
    public abstract void rawOpen();
    public abstract void rawPrint();
    public abstract void rawClose();
}
```

### 4) StringDisplayImpl 클래스
StringDisplayImpl 클래스는 문자열을 표시하는 클래스이다. 단지 표시만 하는 것이 아니라 DisplayImpl 클래스의 하위 클래스로서 rawOpen, rawPrint, rawClose 메소드를 사용해서 표시를 실행한다. DisplayImpl과 StringDisplayImpl 두 클래스가 '구현의 클래스 계층'에 해당된다.

```java
public class StringDisplayImpl extends DisplayImpl {
    private String string;
    private int width;
    public StringDisplayImpl(String string) {
        this.string = string;
        this.width = string.getBytes().length;
    }
    public void rawOpen() {
        printLine();
    }
    public void rawPrint() {
        System.out.println("|" + string + "|");
    }
    public void rawClose() {
        printLine();
    }
    private void printLine() {
        System.out.print("+");
        for (int i = 0; i < width; i++) {
            System.out.print("-");
        }
        System.out.println("+");
    }
}
```

### 5) Main 클래스
앞서 기술한 네 개의 클래스를 조합해서 문자열을 표시한다. 변수 d1에는 Display 클래스의 인스턴스를 대입하고 변수 d2와 d3에는 CountDisplay 클래스의 인스턴스를 대입한다. 둘 다 StringDisplayImpl 클래스의 인스턴스가 구현을 담당한다. d1, d2, d3은 모두 Display 클래스의 인스턴스의 일종이기 때문에 display 메소드를 호출할 수 있고 d3은 multiDisplay 메소드도 호출할 수 있다.

```java
public class Main {
    public static void main(String[] args) {
        Display d1 = new Display(new StringDisplayImpl("Hello, Korea."));
        Display d2 = new CountDisplay(new StringDisplayImpl("Hello, World."));
        CountDisplay d3 = new CountDisplay(new StringDisplayImpl("Hello, Universe."));
        d1.display();
        d2.display();
        d3.display();
        d3.multiDisplay(5);
    }
}
```

## 4. 개념 확장

### 1) 분리해 두면 편해진다.
Bridge 패턴의 특징은 '기능의 클래스 계층'과 '구현의 클래스 계층'을 분리하는 것이다. 이 두 개의 클래스 계층을 분리해 두면 각각의 클래스 계층을 독립적으로 확장할 수 있다.

기능을 추가하고 싶으면 기능의 클래스 계층에 클래스를 추가한다. 이때 구현의 클래스 계층은 전혀 수정할 필요가 없다. 새로 추가한 기능은 '기능 구현'에서 이동할 수 있다.

### 2) 상속은 견고한 연결이고 위임은 느슨한 연결이다.
'상속'은 클래스를 확장하기 위해 편리한 방법이지만 클래스간의 연결을 강하게 고정시킨다. 프로그램의 필요에 따라서 클래스 간의 관계를 바꾸고 싶을 때에 상속을 사용하는 것은 부적절하다. 교체할 때마다 소스 코드를 변경할 수 없기 때문이다. 이와 같은 경우에는 '상속'이 아니라 '위임'을 사용한다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://ko.wikipedia.org/wiki/%EB%B8%8C%EB%A6%AC%EC%A7%80_%ED%8C%A8%ED%84%B4>
