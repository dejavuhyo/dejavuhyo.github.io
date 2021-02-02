---
title: Chain of Responsibility Pattern
author: dejavuhyo
date: 2021-01-14 10:40:00 +0900
categories: [Language, DesignPattern]
tags: [chain-of-responsibility-pattern, chain-of-responsibility, design-pattern, 책임-연쇄-패턴, 디자인-패턴]
---

## 1. Chain of Responsibility 패턴이란
책임 떠넘기기라는 단어는 부정적인 의미가 강하지만 '책임 떠넘기기'같은 처리가 필요 할 경우도 있다. 어떤 요청이 발생했을 때 그 요청을 처리할 오브젝트를 직접 결정할 수 없는 경우, 복수의 오브젝트(객체)를 사슬(chain)처럼 연결해 두면, 그 오브젝트(객체)의 사슬을 차례로 돌아다니면서 목적한 오브젝트(객체)를 결정하는 방법을 생각할 수 있다. 이와 같은 패턴을 _**Chain of Responsibility 패턴**_ 이라고 한다.

responsibility는 책임이라는 의미이므로 '책임을 떠넘기는' 구조를 생각하면 이해하기 쉬울 것이다. 이 패턴을 사용하면 '요청하는 쪽'과 '처리하는 쪽'의 연결을 유연하게 해서 각 오브젝트를 부품으로 독립시킬 수 있다. 또한 상황에 따라서 요청을 처리할 오브젝트가 변하는 프로그램에도 대응할 수 있다.

어떤 사람에게 요구를 한다. 그 사람이 그것을 처리할 수 있으면 처리하고, 처리할 수 없으면 그 요구를 '다음 사람'에게 넘긴다. 다음 사람이 그 요구를 처리할 수 있으면 처리하고, 처리할 수 없으면 '또 다음 사람'에게 책임을 넘긴다. 이것이 Chain of Responsibility 패턴이다.

## 2. 객체

* Chain of Responsibility 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-15-chain-of-responsibility-pattern/img001.png)

### 1) Handler(처리자)의 역할
요구를 처리하는 인터페이스(API)를 결정하는 역할을 한다. '다음 사람'을 준비해 두고 자신이 처리할 수 없는 요구가 나오면 그 사람에게 떠넘기기를 한다. 물론 '다음 사람'도 Handler 역할이다.

### 2) ConcreteHandler(구체적인 처리자)의 역할
요구를 처리하는 구체적인 역할을 한다.

### 3) Client(요구자)의 역할
최초의 ConcreteHandler 역할에 요구하는 일을 한다.

## 3. 예제

### 1) Trouble 클래스
발생한 트러블을 표현하는 클래스이다. number는 트러블 번호이다. getNumber 메소드에서 트러블 번호를 얻는다.

```java
public class Trouble {
    private int number; // 트러블 번호
    public Trouble(int number) { // 트러블의 생성
        this.number = number;
    }
    public int getNumber() { // 트러블 번호를 얻는다
        return number;
    }
    public String toString() { // 트러블의 문자열 표현
        return "[Trouble " + number + "]";
    }
}
```

### 2) Support 클래스
트러블을 해결할 사슬을 만들기 위한 추상 클래스이다. next 필드는 떠넘기는 곳을 지정하고 setNext 메소드는 떠넘기는 곳을 설정한다.

```java
public abstract class Support {
    private String name; // 이 트러블 해결자의 이름
    private Support next; // 떠넘기는 곳
    public Support(String name) { // 트러블 해결자의 생성
        this.name = name;
    }
    public Support setNext(Support next) { // 떠넘기는 곳을 설정
        this.next = next;
        return next;
    }
    public final void support(Trouble trouble) { // 트러블 해결의 수순
        if (resolve(trouble)) {
            done(trouble);
        } else if (next != null) {
            next.support(trouble);
        } else {
            fail(trouble);
        }
    }
    public String toString() { // 문자열 표현
        return "[" + name + "]";
    }
    protected abstract boolean resolve(Trouble trouble); // 해결용 메소드
    protected void done(Trouble trouble) { // 해결
        System.out.println(trouble + " is resolved by " + this + ".");
    }
    protected void fail(Trouble trouble) { // 미해결
        System.out.println(trouble + " cannot be resolved.");
    }
}
```

### 3) NoSupport 클래스
Support 클래스의 하위 클래스이다. NoSupport 클래스의 resolve 메소드는 항상 false를 반환한다. 즉, '자신은 아무것도 문제를 처리하지 않는' 클래스이다.

```java
public class NoSupport extends Support {
    public NoSupport(String name) {
        super(name);
    }
    protected boolean resolve(Trouble trouble) { // 해결용 메소드
        return false; // 자신은 아무것도 처리하지 않는다
    }
}
```

### 4) LimitSupport 클래스
limit에서 지정한 번호 미만의 트러블을 해결하는 클래스이다. reslove 메소드에서는 판단 후에 단지 true 값을 반환하고 있을 뿐이지만, 원래는 여기에서 트러블을 '해결해 줄' 필요가 있다.

```java
public class LimitSupport extends Support {
    private int limit; // 이 번호 미만이면 해결할 수 있다
    public LimitSupport(String name, int limit) { // 생성자
        super(name);
        this.limit = limit;
    }
    protected boolean resolve(Trouble trouble) { // 해결용 메소드
        if (trouble.getNumber() < limit) {
            return true;
        } else {
            return false;
        }
    }
}
```

### 5) OddSupport 클래스
홀수 번호의 트러블을 처리하는 클래스이다.

```java
public class OddSupport extends Support {
    public OddSupport(String name) { // 생성자 
        super(name);
    }
    protected boolean resolve(Trouble trouble) { // 해결용 메소드
        if (trouble.getNumber() % 2 == 1) {
            return true;
        } else {
            return false;
        }
    }
}
```

### 6) SpecialSupport 클래스
지정한 번호의 트러블에 한하여 처리하는 클래스이다.

```java
public class SpecialSupport extends Support {
    private int number; // 이 번호만 해결할 수 있다
    public SpecialSupport(String name, int number) { // 생성자
        super(name);
        this.number = number;
    }
    protected boolean resolve(Trouble trouble) { // 해결용 메소드
        if (trouble.getNumber() == number) {
            return true;
        } else {
            return false;
        }
    }
}
```

### 7) Main 클래스
Alice ~ Fred까지 6명의 해결자를 작성한다. 여기에서는 모두 Support 클래스 형의 변수를 사용하고 있지만, 실제로 대입되는 것은 NoSupport, LimitSupport, OddSupport의 각 클래스의 인스턴스이다.

다음으로 setNext 메소드를 사용해서 Alice ~ Fred를 일렬로 나열한다. 그리고 트러블을 한 개씩 작성해서 순차적으로 alice에게 전달하고, 누가 그 트러블을 해결할지를 표시한다. 여기에서 트러블 번호를 0부터 시작해서 33씩 증가시키고 있지만, 33씩 증가시키는 것에 특별한 의미는 없다.

```java
public class Main {
    public static void main(String[] args) {
        Support alice     = new NoSupport("Alice");
        Support bob     = new LimitSupport("Bob", 100);
        Support charlie   = new SpecialSupport("Charlie", 429);
        Support diana    = new LimitSupport("Diana", 200);
        Support elmo    = new OddSupport("Elmo");
        Support fred     = new LimitSupport("Fred", 300);
        // 연쇄의 형성
        alice.setNext(bob).setNext(charlie).setNext(diana).setNext(elmo).setNext(fred);
        // 다양한 트러블 발생
        for (int i = 0; i < 500; i += 33) {
            alice.support(new Trouble(i));
        }
    }
}
```

## 4. 개념 확장

### 1) 요구하는 사람과 요구를 처리하는 사람을 유연하게 연결한다.
Chain of Responsibility 패턴의 포인트는 요구를 하는 사람(Client 역할)과 요구를 처리하는 사람(ConcreteHandler 역할)을 유연하게 연결하는 것이다. Client 역할은 최초의 사람에게 요구한다. 그러면 뒷일은 사슬 안으로 그 요구가 전달되어 처리자에 의해 요구가 처리된다.

만약, 이 패턴을 사용하지 않으면 '이 요구는 이 사람이 처리해야 한다'라는 정보를 누군가가 중앙집권적으로 가지고 있어야 한다. 그 정보를 '요구를 하는 사람'에게 갖게 하는 것은 별로 현명하지 않다. 요구를 하는 사람이 처리자들의 역할 분담까지 자세하게 알아야 한다면 부품으로써의 독립성이 훼손되기 때문이다.

### 2) 동적으로 사슬의 형태를 바꾼다.
예제 프로그램에서는 Alice에서 Fred까지의 서포트 팀은 항상 고정된 순서로 되어 있었다. 그러나 요구를 처리하는 ConcreteHandler 역할의 오브젝트 관계가 동적으로 변화하는 상황도 생각할 수 있다. Chain of Responsibility 패턴과 같이 위임에 의해 떠넘기기를 실행하고 있으면 상황의 변화에 따라서 ConcreteHandler 역할을 재편할 수 있다.

그러나 Chain of Responsibility 패턴을 사용하지 않고 프로그램 안에 '이 요구라면 이 처리자'라는 대응관계가 고정적으로 기술되어 있으면 프로그램 실행 중에 처리자를 변경하기 어렵게 된다.

윈도우 시스템에서는 사용자가 윈도우 상에 컴포넌트(버튼이나 텍스트 입력 필드)를 자유롭게 추가할 수 있는 경우에 Chain of Responsibility 패턴이 유효하다.

### 3) 자신의 일에 집중할 수 있다.
'떠넘기기'는 부정적인 의미가 강하지만, 반대로 각 오브젝트가 '자신의 일에 집중할 수 있다'는 의미도 된다. 각각의 ConcreteHandler 역할은 자신이 할 수 있는 역할에 집중하고, 자신이 할 수 없으면 간단하게 '다음 사람'에게 전달한다. 이러면 각각의 ConcreteHandler 역할에서 써야 할 처리가 ConcreteHandler 역할의 고유 내용으로 집중할 수 있게 된다.

Chain of Responsibility 패턴을 사용하지 않을 경우에는 '위대한 사람 한 명이 누가 요구를 처리할지 전부 결정'하는 방법으로 한다. 또는 '자신이 처리할 수 없으면 다른 사람에게 맡기고, 만약 그래도 처리할 수 없으면 시스템 상황에 따라서 적절한 사람'에게 처리를 맡기는 '일의 분담'까지 각각의 ConcreteHandler 역할에게 부담시켜야 한다.

### 4) 떠넘기기로 인한 처리 지연 문제
Chain of Responsibility 패넡을 사용해 떠넘기기를 수행하면 틀림없이 유연성은 높을지 모르지만 처리가 지연된다. 누가 요구를 처리할 것인지 미리 정해져 있고 그 상대가 바로 처리하는 경우와 비교하면 Chain of Responsibility 패턴을 적용한 처리는 지연될 것이다. 그러나 이것은 트레이트 오프의 문제이다. 요구와 처리자의 관계가 고정적이고 처리 속도가 상당히 중요한 경우에는 Chain of Responsibility 패턴을 사용하지 않는 편이 유효한 경우도 있다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern>
