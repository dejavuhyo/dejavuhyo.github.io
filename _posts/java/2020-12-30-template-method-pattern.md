--- 
title: Template Method Pattern
author: Hyosik
date: 2020-12-30 09:30:00 +0900
categories: [Language, Java]
tags: [template-method-pattern, template-method, design-pattern, 템플릿-메소드-패턴, 디자인-패턴]
---

## 1. Template Method 패턴이란
상위 클래스쪽에 템플릿에 해당하는 메소드가 정의되어 있고, 그 메소드의 정의 안에는 추상 메소드가 사용되고 있다. 따라서 상위 클래스의 프로그램만 보면 추상 메소드를 어떻게 호출하고 있는지 알 수 있지만, 최종적으로 어떤 처리가 수행되는지는 알 수 없다.

추상 메소드를 실제로 구현하는 것은 하위 클래스이다. 하위 클래스 측에서 메소드를 구현하면 구체적인 처리가 결정된다. 서로 다른 하위 클래스가 서로 다른 구현을 실행하면 서로 다른 처리가 실행될 것이다. 그러나 어떤 하위 클래스에서 어떤 구현을 하더라도 처리의 흐름은 상위 클래스에서 결정한대로 이루어진다.

이와 같이 상위 클래스에서 처리의 뼈대를 결정하고, 하위 클래스에서 그 구체적인 내용을 결정하는 디자인 패턴을 _**Template Method 패턴**_ 이라고 부른다.

## 2. 객체

* Template Method 패턴 클래스 다이어그램

![img001](/assets/img/2020-12-30-template-method-pattern/img001.png)

### 1) AbstractClass(추상 클래스)의 역할
AbstractClass는 템플릿 메소드를 구현한다. 또한 그 템플릿 메소드에서 사요하고 있는 추상 메소드를 선언한다. 이 추상 메소드는 하위 클래스인 ConcreteClass 역할에 의해 구현된다.

### 2) ConcreteClass(구현 클래스)의 역할
AbstractClass 역할에서 정의되어 있는 추상 메소드를 구체적으로 구현한다. 여기에서 구현한 메소드는 AbstractClass역의 템플릿 메소드에서 호출된다.

## 3. 예제

* AbstractDisplay 클래스
  - open, print, close, display라는 메소드를 가지고 있다. 이중에서 open, print, close는 추상 메소드이고 display 메소드만이 구현되고 있다.

```java
public abstract class AbstractDisplay {  // 추상 클래스 AbstractDisplay
    public abstract void open();         // 하위 클래스에 구현을 맡기는 추상 메소드 (1) open
    public abstract void print();        // 하위 클래스에 구현을 맡기는 추상 메소드 (2) print
    public abstract void close();        // 하위 클래스에 구현을 맡기는 추상 메소드 (3) close
    public final void display() {        // 추상 클래스에서 구현되고 있는 메소드 display
        open();                      	 // 우선 open하고…
        for (int i = 0; i < 5; i++) {    // 5번 print을 반복하고…
            print();                    
        }
        close();     // … 마지막으로 close한다. 이것이 display 메소드에서 구현되고 있는 내용.
    }
}
```

* CharDisplay 클래스
  - 상위 클래스인 AbstractDisplay 클래스에서 추상 메소드였던 open, print, close가 모두 구현되어 있기 때문에 CharDisplay 클래스는 추상 클래스가 아니다.

```java
public class CharDisplay extends AbstractDisplay {  // CharDisplay는 AbstractDisplay의 하위 클래스.
    private char ch;                                // 표시해야 할 문자
    public CharDisplay(char ch) {                   // 생성자에서 전달된 문자 ch을
        this.ch = ch;                               // 필드에 기억해 둔다.
    }
    public void open() {                            // 상위 클래스에서는 추상 메소드였다.
						    // 여기에서 오버라이드해서 구현.
        System.out.print("<<");                     // 개시 문자열"<<"을 표시한다.
    }
    public void print() {                           // print 메소드도 여기에서 구현한다.
						    // 이것이 display에서 반복해서 호출된다.
        System.out.print(ch);                       // 필드에 기억해 둔 문자를 1개 표시한다.
    }
    public void close() {                           // close 메소드도 여기에서 구현.
        System.out.println(">>");                   // 종료 문자열 ">>"을 표시.
    }
}
```

* StringDisplay 클래스

```java
public class StringDisplay extends AbstractDisplay {    // StringDisplay도 AbstrctDisplay의 하위 클래스.
    private String string;                              // 표시해야 할 문자열.
    private int width;                                  // 바이트 단위로 계산한 문자열의 「폭」.
    public StringDisplay(String string) {               // 생성자에서 전달된 문자열 string을
        this.string = string;                           // 필드에 기억.
        this.width = string.getBytes().length;          // 그리고 바이트 단위의 폭도 필드에 기억해 두고 나중에 사용한다.
    }
    public void open() {                             // 오버라이드해서 정의한 open 메소드.
        printLine();                                 // 이 클래스의 메소드 printLine에서
                                                     // 선을 그리고 있다.
    }
    public void print() {                               // print 메소드는
        System.out.println(“|” + string + “|”);         // 필드에 기억해 둔 문자열의 전후에 “|”을 붙여서 표시.
    }
    public void close() {                               // close 메소드는
        printLine();                                    // open 처럼 printLine 메소드에서
                                                        // 선을 그리고 있다.
    }
    private void printLine() {                  // open과 close에서 호출된 printLine 메소드이다.
                                                // private이기 때문에 이 클래스 안에서만 사용된다.
        System.out.print(「+「);                // 테두리의 모서리를 표현하는”+” 마크를 표시.
        for (int i = 0; i < width; i++) {       // width개의 “-“을 표시하고
            System.out.print(「-「);            // 테두리 선으로 이용한다.
        }
        System.out.println(「+「);              // 테두리의 모서리를 표현하는 “+” 마크를 표시.
    }
}
```

* Main 클래스

```java
public class Main {
    public static void main(String[] args) {
        // 'H'을 가진 CharDisplay 인스턴스를 1개 만든다.
        AbstractDisplay d1 = new CharDisplay('H');
        // "Hello, world."을 가진 StringDisplay의 인스턴스를 1개 만든다.
        AbstractDisplay d2 = new StringDisplay("Hello, world.");
        // “안녕하세요.”를 가진 StringDisplay의 인스턴스를 1개 만든다.
        AbstractDisplay d3 = new StringDisplay("안녕하세요.");
        d1.display();   // d1, d2, d3 모두 AbstractDisplay의 하위클래스의 인스턴스이기 때문에
        d2.display();   // 상속한 display메소드를 호출할 수 있다.
        d3.display();   // 실제 동작은 CharDisplay나 StringDisplay에서 결정한다.
    }
}
```

## 4. 사용

* 로직을 공통화할 수 있다.
  - 상위 클래스의 템플릿 메소드에서 알고리즘이 기술되어 있으므로, 하위 클래스측에서는 알고리즘을 일일이 기술할 필요가 없다.

* 상위 클래스와 하위 클래스의 연계
  - Template Method 패턴에서는 상위 클래스와 하위 클래스가 긴밀하게 연락을 취하며 작동하고 있다. 따라서 상위 클래스에서 선언된 추상 메소드를 실제로 하위 클래스에서 구현할 때에는 그 메소드가 어느 타이밍에서 호출되는지 이해해야 한다. 상위 클래스의 소스 프로그램이 없으면 하위 클래스의 구현이 어려울 수도 있다.

* 하위 클래스를 상위 클래스와 동일시한다.
  - "상위 클래스형의 변수에 하위 클래스의 어떠한 인스턴스를 대입해도 제대로 작동할 수 있도록 한다"는 원칙은 The Liskov Substitution Principle(LSP)이라고 불린다. LSP는 Template Method 패턴에 국한되지 않는 상속의 일반적인 원칙이다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://mrhook.co.kr/222>
