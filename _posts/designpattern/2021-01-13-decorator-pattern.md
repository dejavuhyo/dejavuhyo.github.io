---
title: Decorator Pattern
author: dejavuhyo
date: 2021-01-13 11:00:00 +0900
categories: [Language, DesignPattern]
tags: [decorator-pattern, decorator, design-pattern, 데코레이터-패턴, 디자인-패턴]
---

## 1. Decorator 패턴이란
스펀지 케이크가 1개 있다고 가정한다. 크림을 바르면 아무 모양이 없는 크림 케이크가 된다. 여기에 딸기를 얹으면 딸기 케이크가 되고, 초콜릿으로 장식을 하고 이름을 쓰고 나이 수만큼 초를 꽂으면 생일 케이크가 완성된다.

오브젝트(객체)도 이런 케이크와 비슷한 경우가 있다. 우선 스펀지 케이크와 같은 중심이 되는 오프젝트가 있다. 이 오브젝트에 장식이 되는 기능을 하나씩 추가하면 좀 더 목적에 맞는 오브젝트가 완성된다. 이와 같이 오브젝트에 장식을 해나가는 디자인 패턴을 _**Decorator 패턴**_ 이라고 한다.

## 2. 객체

* Decorator 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-13-decorator-pattern/img001.png)

### 1) Component의 역할
기능을 추가할 때 핵심이 되는 역할이다. 케이크에 비교하면 장식하기 전의 스펀지 케이크에 해당한다. Component 역할은 스펀지 케이크의 인터페이스(API)만을 결정한다.

### 2) ConcreteComponent의 역할
Component 역할의 인스턴스(API)를 구현하고 있는 구체적인 스펀지 케이크이다.

### 3) Decorator(장식자)의 역할
Component 역할과 동일한 인터페이스(API)를 가지며, 또한 이 Decorator 역할이 장식할 대상이 되는 Component 역할도 가지고 있다. 이 역할은 자신이 장식하고 있는 대상을 알고 있다.

### 4) ConcreteDecorator(구체적인 장식자)의 역할
구체적인 Decorator의 역할이다.

## 3. 예제

### 1) Display 클래스
복수 행으로 구성되는 문자열을 표시하기 위한 추상 클래스이다.

getColumns와 getRows는 각각 가로의 문자수와 세로의 문자수를 얻기 위한 메소드이다. 이것은 추상 메소드로서 하위 클래스에서 구현되어야 한다(subclass responsibility). getRowText는 지정한 행의 문자열을 얻는 메소드이다. 이것도 추상 메소드로서 하위 클래스에서 구현되어야 한다.

show는 모든 행을 표시하는 메소드이다. 이 안에서는 getRows 메소드로 행수를 취득하며 getRowText 메소드로 표시해야 할 문자열을 취득하고, for 루프를 사용해서 모든 행을 표시한다. show는 getRows와 getRowText라는 추상 메소드를 사용한 Template Method 패턴으로 되어 있다.

```java
public abstract class Display {
    public abstract int getColumns();
    public abstract int getRows();
    public abstract String getRowText(int row);
    public void show() {
        for (int i = 0; i < getRows(); i++) {
            System.out.println(getRowText(i));
        }
    }
}
```

### 2) StringDisplay 클래스
1행의 문자열을 표시하는 클래스이다. StringDisplay 클래스는 Display 클래스의 하위 클래스이기 때문에, Display 클래스에서 선언된 추상 메소드를 구현할 책임이 있다.

string 필드는 표시할 문자열을 저장한다. StringDisplay 클래스에서 표시하는 것은 string 필드의 내용 1행 뿐이기 때문에 getColumns는 string.getBytes().length의 값을 반환하고 getRows는 1을 반환한다. 또한 getRowText는 0번째의 값을 취할 때만 string 필드를 반환한다.

```java
public class StringDisplay extends Display {
    private String string;
    public StringDisplay(String string) {
        this.string = string;
    }
    public int getColumns() {
        return string.getBytes().length;
    }
    public int getRows() {
        return 1;
    }
    public String getRowText(int row) {
        if (row == 0) {
            return string;
        } else {
            return null;
        }
    }
}
```

### 3) Border 클래스
'장식'을 나타내는 추상 클래스이다. 장식을 표시하는 클래스이지만 문자열 표시를 실행하는 Display 클래스의 하위 클래스로 정의한다. 즉, 상속에 의해 장식은 내용물과 동일한 메소드를 가진다. 

구체적으로 말하면 Border 클래스는 getColumns, getRows, getRowText, show의 각 메소드를 상속하고 있다는 의미이다. 장식(Border)이 내용물(Display)과 같은 메소드를 가진다는 것은 인터페이스(API)적으로 장식과 내용물을 동일시할 수 있다는 의미이다.

```java
public abstract class Border extends Display {
    protected Display display;
    protected Border(Display display) {
        this.display = display;
    }
}
```

### 4) SideBorder 클래스
구체적인 장식의 일종으로 Border클래스의 하위 클래스이다. SideBorder 클래스는 문자열의 좌우에 정해진 문자(borderChar)로 장식을 한다.

SideBorder는 추상 클래스가 아니다. 왜냐하면 상위 클래스에서 선언되어 있던 추상 메소드가 모두 여기에서 구현되고 있기 때문이다.

```java
public class SideBorder extends Border {
    private char borderChar;
    public SideBorder(Display display, char ch) {
        super(display);
        this.borderChar = ch;
    }
    public int getColumns() {
        return 1 + display.getColumns() + 1;
    }
    public int getRows() {
        return display.getRows();
    }
    public String getRowText(int row) {
        return borderChar + display.getRowText(row) + borderChar;
    }
}
```

### 5) FullBorder 클래스
FullBorder 클래스도 SideBorder 클래스와 같이 Border의 하위 클래스 중 하나이다. SideBorder 클래스는 좌우에만 장식을 했지만 FullBorder 클래스는 상하좌우에 장식을 한다. 단, SideBorder 클래스에서는 장식할 문자를 지정할 수 있었지만, FullBorder 클래스에서는 장식할 문자가 고정되어 있다. makeLine 메소드는 지정한 문자가 연속되는 문자열을 만드는 보조용 메소드이다(클래스 외부에서 사용할 수 없도록 private로 되어있다).

```java
public class FullBorder extends Border {
    public FullBorder(Display display) {
        super(display);
    }
    public int getColumns() {
        return 1 + display.getColumns() + 1;
    }
    public int getRows() {
        return 1 + display.getRows() + 1;
    }
    public String getRowText(int row) {
        if (row == 0) {
            return "+" + makeLine('-', display.getColumns()) + "+";
        } else if (row == display.getRows() + 1) {
            return "+" + makeLine('-', display.getColumns()) + "+";
        } else {
            return "|" + display.getRowText(row - 1) + "|";
        }
    }
    private String makeLine(char ch, int count) {
        StringBuffer buf = new StringBuffer();
        for (int i = 0; i < count; i++) {
            buf.append(ch);
        }
        return buf.toString();
    }
}
```

### 6) Main 클래스
동작 테스트용 클래스이다.

```java
public class Main {
    public static void main(String[] args) {
        Display b1 = new StringDisplay("Hello, world.");
        Display b2 = new SideBorder(b1, '#');
        Display b3 = new FullBorder(b2);
        b1.show();
        b2.show();
        b3.show();
        Display b4 = 
                    new SideBorder(
                        new FullBorder(
                            new FullBorder(
                                new SideBorder(
                                    new FullBorder(
                                        new StringDisplay("안녕하세요.")
                                    ),
                                    '*'
                                )
                            )
                        ),
                        '/'
                    );
        b4.show();
    }
}
```

## 4. 개념 확장

### 1) 투과적인 인터페이스(API)
Decorator 패턴에서는 장식과 내용물을 동일시하고 있다. 장식을 사용해서 내용물을 감싸도 인터페이스(API)는 전혀 감출 수 없다. getColumns, getRows, getRowText, show라는 메소드는 감추어지는 일없이 다른 클래스에서 볼 수 있다. 이것을 인터페이스(API)가 '투과적'이라고 한다.

인터페이스(API)가 투과적이기 때문에 Decorator 패턴에서는 Composite 패턴과 닮은 재귀적인 구조가 등장한다. 즉, 장식이 둘러싸고 있는 '내용물'이 실제로는 다른 '장식'이 되는 구조이다. Decorator 패턴과 Composite 패턴은 재귀적인 구조를 취하는 점에서는 유사하지만 목적은 다르다. Decorator 패턴은 테두리 장식을 중복해서 기능을 추가해가는 것에 주안점을 두기 때문이다.

### 2) 내용물을 바꾸지 않고 기능을 추가할 수 있다.
Decorator 패턴에서는 장식도 내용물도 공통의 인터페이스(API)를 가진다. 인터페이스(API)는 공통이지만, 장식하면 장식할수록 기능이 추가된다. Display를 SideBorder로 감싸면 좌우에 새로운 장식문자를 표시할 수 있다. 그리고 FullBorder로 감싸면 가장자리 전체에 장식을 붙일 수 있다. 이때, 장식되는 상대(내용물)는 수정할 필요가 없다. 즉, 내용물을 변경하지 않고 기능을 추가할 수 있다.

Decorator 패턴에서는 위임을 사용하고 있다. '장식'에 대한 요구(메소드의 호출)는 그 '내용물'에 떠넘겨(위임)진다.

### 3) 동적인 기능을 추가할 수 있다.
Decorator 패턴에서 사용되는 위임은 클래스 사이를 느슨하게 결합한다. 따라서 framework의 소스를 변경하지 않고 오브젝트(객체)의 관계를 변경한 새로운 오프젝트(객체)를 만들 수 있다.

### 4) 단순한 장식이라도 다양한 기능을 추가할 수 있다.
Decorator 패턴을 사용하면 다양한 기능을 추가할 수 있다. 구체적인 장식(ConcreteDecorator 역할)을 많이 준비해 두면, 그것들을 자유롭게 조합해서 새로운 오브젝트를 만들 수 있기 때문이다. 이때, 각각의 장식은 단순해도 상관없다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://en.wikipedia.org/wiki/Decorator_pattern>
