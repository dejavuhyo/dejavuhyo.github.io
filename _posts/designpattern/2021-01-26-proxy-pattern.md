---
title: Proxy Pattern
author: dejavuhyo
date: 2021-01-26 14:00:00 +0900
categories: [Language, DesignPattern]
tags: [proxy-pattern, proxy, design-pattern, 프록시-패턴, 디자인-패턴]
---

## 1. Proxy 패턴이란
proxy는 '대리인'이라는 의미이다. 대리인이란 일을 해야할 본일을 대신(대리)하는 사람이다. 본인이 아니라도 가능한 일을 맡기기 위해서 대리인을 세운다. 대리인은 어디까지나 대리에 지나지 않기 때문에 할 수 있는 일에는 한계가 있다. 대리인이 할 수 있는 범위를 넘는 일이 발생하면, 대리인은 본인한테 와서 상담을 한다.

오브젝트(객체) 지향에서는 '본인'도 '대리인'도 오브젝트(객체)가 된다. 바빠서 일을 할 수 없는 본인 오브젝트 대신에 대리인 오브젝트가 어느 정도 일을 처리하게 된다. 이와 같은 디자인 패턴을 _**Proxy 패턴**_ 이라고 한다.

## 2. 객체

* Proxy 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-26-proxy-pattern/img001.png)

### 1) Subject(주제)의 역할
Proxy 역할과 RealSubject 역할을 동일시하기 위한 인터페이스(API)를 결정한다. Subject 역할이 있는 덕분에 Client 역할은 Proxy 역할과 RealSubject 역할의 차이를 의식할 필요가 없다.

### 2) Proxy(대리인)의 역할
Client 역할의 요구를 할 수 있는 만큼 처리를 한다. 만약, 자신망으로 처리할 수 없으면 Proxy 역할은 RealSubject 역할에게 처리를 맡긴다. Proxy 역할은 정말로 RealSubject 역할이 필요해지면 그때 RealSubject 역할을 생성한다. Proxy 역할은 Subject 역할에서 정해지는 인터페이스(API)를 구현한다.

### 3) RealSubject(실제의 주체)의 역할
'대리인'인 Proxy 역할에서 감당할 수 없는 일이 발생했을 때 등장하는 것이 '본인'인 RealSubject 역할이다. 이 역할도 Proxy 역할과 마찬가지로 Subject 역할에서 정해져 있는 인터페이스(API)를 구현한다.

### 4) Client(의뢰인)의 역할
Proxy 패턴을 이용하는 역할이다.

## 3. 예제

### 1) Printer 클래스
'본인'을 표시하는 클래스이다. 생성자에서는 더미의 '무거운 일' 즉, heavyJob을 실행하고 있다. setPrinterName은 이름을 설정하는 메소드이고, getPrinterName은 이름을 취득하는 메소드이다. print 메소드는 프린터의 이름을 붙여서 문자열을 표시하고 있다. heavyJob 메소드는 실행에 5초가 걸리는 무거운 일을 표현하고 있다. 1초(1000ms)에 한번 마침표(.)를 표시한다.

Proxy 패턴의 중심은 PrinterProxy 클래스이다. Printer 클래스에서 특별히 어려운 문제는 없다.

```java
public class Printer implements Printable {
    private String name;
    public Printer() {
        heavyJob("Printer의 인스턴스를 생성 중");
    }
    public Printer(String name) { // 생성자
        this.name = name;
        heavyJob("Printer의 인스턴스 (" + name + ")을 생성 중");
    }
    public void setPrinterName(String name) { // 이름의 설정
        this.name = name;
    }
    public String getPrinterName() { // 이름의 취득
        return name;
    }
    public void print(String string) { // 이름을 붙여 표시
        System.out.println("=== " + name + " ===");
        System.out.println(string);
    }
    private void heavyJob(String msg) { // 무거운 일(의 예정)
        System.out.print(msg);
        for (int i = 0; i < 5; i++) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
            }
            System.out.print(".");
        }
        System.out.println("완료");
    }
}
```

### 2) Printable 인터페이스
PrinterProxy 클래스와 Printer 클래스를 동일시 하기 위한 것이다. setPrinterName 메소드는 이름의 설정, getPrinterName 메소드는 이름의 취득, 그리고 print 메소드는 프린트 아웃(문자열 표시)을 위한 것이다.

```java
public interface Printable {
    public abstract void setPrinterName(String name); // 이름의 설정
    public abstract String getPrinterName(); // 이름의 취득
    public abstract void print(String string); // 문자열 표시(프린트 아웃)
}
```

### 3) PrinterProxy 클래스
대리인의 역할을 수행하며, Printable 인터페이스를 구현한다. name 필드는 이름을 저장하고, real 필드는 '본인'을 저장한다. 생성자는 이름을 설정한다(이 시점에서 '본인'은 아직 만들어지지 않았다).

기억해야 할 점은 Printer 클래스는 PrinterProxy의 존재를 모른다는 점이다. 자신이 PrinterProxy을 경유해서 호출되고 있는지 아니면 직접 호출되고 있는지 Printer 클래스는 모른다. 반면에 PrinterProxy 클래스는 Printer 클래스를 알고 있다. 왜냐하면 PrinterProxy 클래스의 real 필드는 Printer형이고, PrinterProxy 클래스의 소스 코드 안에는 Printer라는 클래스 이름이 기술되어 있기 때문이다. 이처럼 PrinterProxy 클래스는 Printer 클래스와 깊이 관려된 부품이다.

```java
public class PrinterProxy implements Printable {
    private String name; // 이름
    private Printer real; // 「본인」
    public PrinterProxy() {
    }
    public PrinterProxy(String name) { // 생성자
        this.name = name;
    }
    public synchronized void setPrinterName(String name) { // 이름의 설정
        if (real != null) {
            real.setPrinterName(name); // 「본인」에게도 설정한다
        }
        this.name = name;
    }
    public String getPrinterName() { // 이름의 설정
        return name;
    }
    public void print(String string) { // 표시
        realize();
        real.print(string);
    }
    private synchronized void realize() { // 「본인」을 생성
        if (real == null) {
            real = new Printer(name);
        }
    }
}
```

### 4) Main 클래스
PrinterProxy를 경우해서 Printer를 이용하는 클래스이다. 이 클래스는 처음에 PrinterProxy를 생성하고, getPrinterName을 이용해서 이름을 표시한다. 그리고 나서 setPrinterName으로 이름을 설정하고, 마지막에 printfh "Hello, world"라고 표시한다.

```java
public class Main {
    public static void main(String[] args) {
        Printable p = new PrinterProxy("Alice");
        System.out.println("이름은 현재 " + p.getPrinterName() + "입니다.");
        p.setPrinterName("Bob");
        System.out.println("이름은 현재 " + p.getPrinterName() + "입니다.");
        p.print("Hello, world.");
    }
}
```

## 4. 개념 확장

### 1) 대리인을 사용해서 속도 올리기
Proxy 패턴에서는 Proxy 역할이 대링인이 되어 가능한 일만 처리를 대신한다. 예제 프로그램에서는 Proxy 역할을 사용해서 실제로 print할 때까지 무거운 처리(인스턴스 생성)를 지연시킬 수 있었다.

이 예제 프로그램에서는 무거운 처리라고 해봐야 뻔한 일이기 때문에 별로 도움이 되지 않는다. 그러나 초기화에 시간이 걸리는 기능이 많이 존재하는 대규모 시스템을 생각해보자. 기동 시점에서는 이용하지 않는 기능까지 전부 초기화하면, 어플리케이션의 기동에 시간이 많이 걸리는 문제가 발생한다. 이러면 사용자가 불만을 느끼게 된다. 따라서 실제로 그 기능을 사용할 단계가 되어 처음으로 초기화를 하는 편이 사용자의 스트레스를 줄여주는 방법이 될 것이다.

### 2) 대리인과 본인을 불리할 필요성
PrinterProxy 클래스와 Printer 클래스로 분리하지 않고 Printer 클래스 안에 처음부터 지연평가의 기능(필요하게 되면 그때 인스턴스를 생성하는 기능)을 넣을 수도 있다. 그러나 Proxy 역할과 RealSubject 역할을 분리해서 프로그램의 부품화를 만들면, 개별적으로 수정을 할 수 있다.

PrinterProxy 클래스의 구현을 바꾸면 Printable 인터페이스에 선언되어 있는 메소드 중에서 무엇을 대리인이 처리하고 무엇을 본인이 처리할 것인지를 변경할 수 있다. 게다가 이와 같은 수정을 마무리해도 Printer 클래스 쪽은 수정할 필요가 전혀 없다. 만약, 지연평가를 실행시키고 싶지 않으면, Main 클래스에 PrinterProxy 클래스의 인스턴스를 new하지 않고 Printer 클래스의 인스턴스를 new하면 된다. PrinterProxy 클래스와 Printer 클래스 모두 Printable 인터페이스를 구현하고 있기 때문에, Main 클래스는 안심하고 PrinterProxy와 Printer를 교체해서 사용할 수 있다.

PrinterProxy 클래스는 'Proxy 역할'이란 기능을 표현하고 있다. 따라서, 그 기능을 이요할지/이용하지 않을지의 선택은 PrinterProxy를 사용할지, 사용하지 않을지로 표현된다.

### 3) 대리와 위임
대리인이 혼자 처리할 수 있는 일은 대리인이 처리한다. 대리인이 처리할 수 없을 때에는 처리할 수 있는 본인에게 '위임'한다. 이 '위임'은 여러 곳에서 등자하는 '위임'으로, PrinterProxy 클래스의 print에서 real.print를 호출하고 있는 바로 그 '위임'이다.

### 4) 투과적이란
PrinterProxy 클래스와 Printer 클래스는 같은 Printable이란 인터페이스를 구현하고 있다. Main 클래스는 실제로 호출하는 곳이 PrinterProxy 클래스이든 Printer 클래스이든 상관하지 않는다. Printer를 직접 이용해도, 중간에 PrinterProxy가 들어와도 문제없이 사용할 수 있다.

이와 같은 경우, PrinterProxy 클래스는 '투과적'이라고 말할 수 있다. Main 클래스와 Printer 클래스 사이에 PrinterProxy 클래스가 놓여있어도 문제가 되지 않는다.

### 5) HTTP Proxy
HTTP Proxy는 HTTP 서버(웹 서버)와 HTTP 클라이언트(웹 브라우저) 사이에서 웹 페이지의 캐싱(cashing)을 실행하는 소프트웨어이다. 이것도 Proxy 패턴을 적용해서 생각할 수 있다.

HTTP Proxy는 많은 기능을 가지고 있기 때문에 페이지의 캐싱(cashing)에 대해 설명한다.

웹 브라우저가 어떤 웹 페이지를 표시할 때 일일이 원격지에 있는 웹 서버에 액세스해서 그 페이지를 취득하는 것이 아니고, HTTP Proxy가 캐쉬해서 어떤 페이지를 대신해서 취득한다. 최신 정보가 필요하거나 페이지의 유효기간이 지났을 때 웹 서버에 웹 페이지를 가지러 간다.

여기에서는 웹 브라우저가 Client 역할, HTTP Proxy가 Proxy 역할, 그리고 웹 서버가 RealSubcjet 역할을 한다.

### 6) Proxy 종류
Proxy 패턴에는 다양한 종류가 있다.

* Virtual Proxy(가상 프록시)
  - 여기에서 설명한 Proxy 패턴이다. 정말로 인스턴스가 필요한 시점에서 생성, 초기화를 실행한다.

* Remote Proxy(원격 프록시)
  - RealSubject 역할이 네트위크의 상대 쪽에 있음에도 불구하고 마치 자신의 옆에 있는 것처럼(투과적으로) 메소드를 호출할 수 있다. Java의 RMI(Remote Method Invocation:원격 메소드  호출) 등이 여기에 해당한다.

* Access Proxy
  - RealSubject 역할의 기능에 대해서 액세스 제한을 설정한 것이다. 정해진 사용자이면 메소드 호출을 허가하지만, 그외에는 에러로 처리하는 Proxy이다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://en.wikipedia.org/wiki/Proxy_pattern>
