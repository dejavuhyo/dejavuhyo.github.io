--- 
title: Adapter Pattern
author: Hyosik
date: 2020-12-29 09:30:00 +0900
categories: [Language, Java]
tags: [adapter-pattern, adapter, design-pattern, 어댑터-패턴, 디자인-패턴]
---

## 1. Adapter 패턴이란
이미 제공되어 있는 것을 그대로 사용할 수 없을 때, 필요한 형태로 교환하고 사용하는 일이 자주 있다. '이미 제공되어 있는 것'과 '필요한 것' 사이의 '차이'를 없애주는 디자인 패턴이 _**Adapter 패턴**_ 이다.

Adapter 패턴은 Wrapper 패턴으로 불리기도 한다. wrapper는 '감싸는 것'이라는 의미이다. 일반 상품을 예쁜 포장지로 싸서 선물용 상품으로 만드는 것처럼, 무엇인가를 한번 포장해서 다른 용도로 사용할 수 있게 교환해 주는 것이 wrapper이며 adapter이다.

Adapter 패턴에는 두 가지 종류가 있다.

* 클래스에 의한 Adapter 패턴(상속을 사용한 Adapter 패턴)

* 인스턴스에 의한 Adapter 패턴(위임을 사용한 Adapter 패턴)

## 2. 객체

* 클래스에 의한 Adapter 패턴의 클래스 다이어그램(상속을 사용)

![img001](/assets/img/2020-12-29-adapter-pattern/img001.png)

* 인스턴스에 의한 Adapter 패턴의 클래스 다이어그램(위임을 사용)

![img002](/assets/img/2020-12-29-adapter-pattern/img002.png)

### 1) Target(대상)의 역할
지금 필요한 메소드를 결정한다.

### 2) Client(의뢰자)의 역할
Target 역할의 메소드를 사용해서 일을 한다.

### 3) Adaptee(개조되는 쪽)의 역할
이미 준비되어 있는 메소드를 가지고 있는 역할을 한다.

### 4) Adapter의 역할
Adaptee 역할의 메소드를 사용해서 어떻게든 Target 역할을 만족시키기 위한 것이 Adapter 패턴의 목적이며, Adapter 역할의 임무이다.

클래스에 의한 Adapter 패턴의 경우에는 Adapter의 역할은 '상속'을 사용한 Adaptee의 역할을 이용하지만, 인스턴스에 의한 Adapter 패턴의 경우에는 '위임'을 사용한 Adaptee의 역할을 이용한다.

## 3. 예제

### 1) 상속을 사용한 Adapter 패턴

* Banner 클래스

```java
public class Banner {
    private String string;
    public Banner(String string) {
        this.string = string;
    }
    public void showWithParen() {
        System.out.println("(" + string + ")");
    }
    public void showWithAster() {
        System.out.println("*" + string + "*");
    }
}
```

* Print 인터페이스

```java
public interface Print {
    public abstract void printWeak();
    public abstract void printStrong();
}
```

* PrintBanner 클래스
  - PrintBanner 클래스가 어댑터의 역할을 완수한다. 준비된 Banner 클래스를 확장(extends)해서 showWithParen 메소드와 showWithAster 메소드를 상속한다. 또한, 필요한 Print 인터페이스를 구현(implements)해서 printWeak 메소드와 printStrong 메소드를 구현하고 있다.

```java
public class PrintBanner extends Banner implements Print {
    public PrintBanner(String string) {
        super(string);
    }
    public void printWeak() {
        showWithParen();
    }
    public void printStrong() {
        showWithAster();
    }
}
```

* Main 클래스
  - 어댑터 역할의 PrintBanner 클래스를 사용해서 Hello라는 문자열을 약하게(갈호를 붙임), 또는 강하게(* 사이에) 표시한다.

```java
public class Main {
    public static void main(String[] args) {
        Print p = new PrintBanner("Hello");
        p.printWeak();
        p.printStrong();
    }
}
```

### 2) 위임을 사용한 Adapter 패턴
Banner 클래스를 이용해서 Print 클래스와 동일한 메소드를 갖는 클래스를 실현하는 것이다. Java에서는 2개의 클래스를 동시에 상속할 수 없기 때문에(단일 상속), PrintBanner 클래스를 Print와 Banner 모두의 하위 클래스로 정의할 수 없다.

PrintBanner 클래스는 banner 필드에서 Banner 클래스의 인스턴스를 가진다. 이 인스턴스는 PrintBanner 클래스의 생성자에서 생성한다. 그리고 printWeak 및 printStrong 메소드에서는 banner 필드를 매개로 showWithParen, showWithAster 메소르를 호출한다.

PrintBanner 클래스의 printWeak 메소드가 호출되었을 때, 자신이 처리하는 것이 아니라 별도의 인스턴스(Banner의 인스턴스)인 showWithParen 메소드에게 위임하고 있다.

* Print 클래스

```java
public abstract class Print {
    public abstract void printWeak();
    public abstract void printStrong();
}
```

* PrintBanner 클래스

```java
public class PrintBanner extends Print {
    private Banner banner;
    public PrintBanner(String string) {
        this.banner = new Banner(string);
    }
    public void printWeak() {
        banner.showWithParen();
    }
    public void printStrong() {
        banner.showWithAster();
    }
}
```

## 4. 사용
Adapter 패턴은 기존의 클래스를 개조해서 필요한 클래스를 만든다. 이 패턴으로 필요한 메소드를 빠르게 만들 수 있다. 만약 버그가 발생해도 기존의 클래스(Adaptee의 역할)에는 버그가 없으므로 Adapter 역할의 클래스를 중점적으로 조사하면 되고, 프로그램 검사도 상당히 쉬워진다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://jusungpark.tistory.com/22>
