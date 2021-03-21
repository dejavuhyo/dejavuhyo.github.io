---
title: Observer Pattern
author: dejavuhyo
date: 2021-01-20 10:00:00 +0900
categories: [Language, DesignPattern]
tags: [observer-pattern, observer, design-pattern, 옵서버-패턴, 디자인-패턴]
---

## 1. Observer 패턴이란
Observer 패턴에서는 관찰 대상의 상태가 변화하면 관찰자에게 알려준다. _**Observer 패턴**_ 은 상태 변화에 따른 처리를 기술할 때 효과적이다.

## 2. 객체

* Observer 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-20-observer-pattern/img001.png)

### 1) Subject(관찰 대상자)의 역할
Subject 역할은 관찰자인 Observer 역할을 동록하는 메소드와 삭제하는 메소드를 가지고 있다. 또 '현재의 상태를 취득하는' 메소드도 선언되어 있다.

### 2) ConcreteSubject(구체적인 관찰 대상자)의 역할
상태가 변화하면 그것이 등록되어 있는 Observer 역할에 전한다. 

### 3) Observer(관찰자)의 역할
Subject 역할로부터 '상태가 변했다'라고 전달 받는 역할을 한다. 이를 위한 메소드는 update이다.

### 4) ConcreteObserver(구체적인 관찰자)의 역할
update 메소드가 호출되면 그 메소드 안에서 Subject 역할의 현재 상태를 취득한다.

## 3. 예제

### 1) Observer 인터페이스
구체적인 관찰자는 이 인터페이스를 구현한다. 또한, Observer 인터페이스는 예제 프로그램용으로 만들었으며, Java의 클래스 라이브러리 java.util.Observer와는 다르다.

```java
public interface Observer {
    public abstract void update(NumberGenerator generator);
}
```

### 2) NumberGenerator 클래스
수를 생성하는 추상 클래스이다. 실제의 수의 생성(execute 메소드)과 수를 취득하는 부분(getNumber 메소드)은 하위 클래스에서 구현되도록 추상 메소드로 되어 있다(subclass responsibility).

```java
import java.util.ArrayList;
import java.util.Iterator;

public abstract class NumberGenerator {
    private ArrayList observers = new ArrayList(); // Observer들을 저장
    public void addObserver(Observer observer) { // Observer을 추가
        observers.add(observer);
    }
    public void deleteObserver(Observer observer) { // Observer을 삭제
        observers.remove(observer);
    }
    public void notifyObservers() { // Observer에 통지
        Iterator it = observers.iterator();
        while (it.hasNext()) {
            Observer o = (Observer)it.next();
            o.update(this);
        }
    }
    public abstract int getNumber(); // 수를 취득한다
    public abstract void execute(); // 수를 생성한다
}
```

### 3) RandomNumberGenerator 클래스
NumberGenerator의 하위 클래스이고, 난수를 생성한다. random 필드에는 java.util.Random 클래스의 인스턴스(말하자면 난수발생기)가 저장되고, number 필드에는 현재의 난수값이 저장된다. getNumber 메소드는 number 필드의 값을 반환한다.

```java
import java.util.Random;

public class RandomNumberGenerator extends NumberGenerator {
    private Random random = new Random(); // 난수발생기
    private int number; // 현재의 수
    public int getNumber() { // 수를 취득한다
        return number;
    }
    public void execute() {
        for (int i = 0; i < 20; i++) {
            number = random.nextInt(50);
            notifyObservers();
        }
    }
}
```

### 4) DigitObserver 클래스
Observer 인터페이스를 구현하는 클래스로 관찰한 수를 '숫자'로 표시하기 위한 것이다. update 메소드 안에서 인수로 주어진 NumberGenerator의 getNumber 메소드를 사용해서 수를 취득하고 그것을 표시한다. 여기에서는 표시되는 모습을 잘 알 수 있도록 Thread.sleep을 사용해서 속도를 느리게 하고 있다.

```java
public class DigitObserver implements Observer {
    public void update(NumberGenerator generator) {
        System.out.println("DigitObserver:" + generator.getNumber());
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
        }
    }
}
```

### 5) GraphObserver 클래스
Observer 인터페이스를 구현하는 클래스이다. 이 클래스는 관찰한 수를 '간이 그래프'로 표시한다.

```java
public class GraphObserver implements Observer {
    public void update(NumberGenerator generator) {
        System.out.print("GraphObserver:");
        int count = generator.getNumber();
        for (int i = 0; i < count; i++) {
            System.out.print("*");
        }
        System.out.println("");
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
        }
    }
}
```

### 6) Main 클래스
RandomNumberGenerator의 인스턴스를 한 개 만들고, 그 관찰자를 두 개 만든다. observer1은 DigitObserver, observer2는 GraphObserver의 인스턴스이다. addObserver 메소드를 사용해서 관찰자를 등록한 후 generator.execute를 사용해서 수를 생성한다.

```java
public class Main {
    public static void main(String[] args) {
        NumberGenerator generator = new RandomNumberGenerator();
        Observer observer1 = new DigitObserver();
        Observer observer2 = new GraphObserver();
        generator.addObserver(observer1);
        generator.addObserver(observer2);
        generator.execute();
    }
}
```

## 4. 개념 확장

### 1) 교환 가능성
디자인 패턴의 목적 중의 하나는 클래스를 재이용 가능한 부품으로 만드는 일이다. Observer 패턴에서는 상태를 가지고 있는 ConcreteSubject 역할과 상태변화를 전달 받는 ConcreteObserver 역할이 등장했다. 그리고 이 두 가지의 역할을 연결하는 것이 인터페이스(API)인 Subject 역할과 Observer 역할이다.

RandomNumberGenerator 클래스는 현재 자신을 관찰하고 있는 것이(자신이 열려주는 상대가) DigitObserver 클래스의 인스턴스인지 GraphObserver 클래스의 인스턴스인지 몰라도 상관없다. 그러나 observer 필드에 저장되어 있는 인스턴스들이 Observer 인터페이스를 구현하고 있다는 것은 알고 있다. 이 인스턴스들은 addObserver에서 추가된 것이므로 반드시 Observer 인터페이스를 구현하고 있으며 update 메소드를 호출할 수 있다.

한편, DigitObserver 클래스는 자신이 관찰하고 있는 것이 RandomNumberGenerator 클래스의 인스턴스인지, 다른 XXXNumberGenerator 클래스의 인스턴스인지 신경 쓰지 않는다. 단지 NumberGenerator의 하위 클래스의 인스턴스이고, getNumber 메소드를 가지고 있다는 것은 알고 있다,

> 추상 클래스나 인터페이스를 사용해서 구상 클래스로부터 추상 메소드를 분리한다.
>
> 인수로 인스턴스를 전달할 때, 필드에서 인스턴스를 저장할 때에는 구상 클래스의 형태로 하지 않고 추상 클래스나 인터페이스의 형태로 해 둔다.

### 2) Observer의 순서
Subject 역할에는 복수의 Observer 역할이 등록되어 있다. 예제 프로그램의 notifyObserver 메소드에서는 먼저 등록한 Observer의 update 메소드가 먼저 호출된다.

일반적으로 ConcreteObserver 역할의 클래스를 설계할 경우에는 update 메소드가 호출되는 순서가 변해도 문제가 일어나지 않도록 해야한다. DigitObserver의 update를 호출한 후가 아니라면 GraphObserver의 update가 바르게 동작해야 한다. 원래 각 클래스의 독립성이 보장되면 의존성의 혼란은 별로 발생하지 않는다.

### 3) Observer의 행위가 Subject에 영향을 미칠 때
예제 프로그램에서는 RandomNumberGenerator가 자신 안에서 데이터를 생성하고 update 메소드를 호출했다. 그러나 일반적인 Observer 패턴에서는 Subject 역할이 다른 클래스로부터 update 메소드의 호출을 요청받는 경우도 있다. 그래픽 유저 인터페이스(GUI)에서는 사용자가 버튼을 눌러 요청하면 update 메소드가 호출되는 경우가 자주 있다.

그런데 Subject 역할이 update 메소드를 호출할 때 Observer 역할이 호출을 요청하는 경우도 있다. 이와 같은 경우 조심해서 설계하지 않으면 메소드 호출이 영원히 계속될 가능성이 있다. 이것을 방지하기 위해서는 Observer 역할에게 '현재 Subject 역할로부터 전달받고 있는 중인지 아닌지'를 나타내는 플래그 변수를 한 개 갖도록 하는 것이 좋다.

### 4) 갱신을 위한 힌트 정보의 취득
NumberGenerator는 update 메소드를 사용해서 '갱신되었다'고 Observer에게 통지하고 있다. update 메소드의 인수로 주어지고 있는 것은 호출을 행한 NumberGenerator의 인스턴스 뿐이었다. Observer는 update 메소드 안에서 getNumber를 호출해서 필요한 정보를 얻어야 한다.

예제 프로그램보다 복잡한 프로그램의 경우, 정말 Observer 역할에게 필요한 정보는 무엇인지 Subject 역할에게 알리는 것은 어려운 문제이다. 예를 들어, 이전 update 메소드를 호출하고 나서 현재까지의 사이에 갱신된 정보만을 보낸다고 하자. 이때 Subject 역할은 시간 계산을 해야 한다. 따라서, 어느 정도의 힌트 정보를 update 메소드에게 제공할지는 프로그램의 복잡성을 고려해서 판단해야 한다.

### 5) '관찰'하기보다 '전달'받길 기다린다
observer는 '관찰자'라는 의미이지만 실제로 Observer는 능동적으로 '관찰'하는 것이 아니고, Subject 역할로부터 '전달'되는 것을 수동적으로 기다리고 있다. Observer 패턴은 Publish-Subscribe 패턴이라고도 한다. publish(발행)와 subscribe(구독)라는 표현이 더 적당할 수도 있다.

### 6) Model, View, Controller(MVC)
MVC 안의 Model과 View의 관계는 Observer 패턴의 Subject 역할과 Observer 역할의 관계에 대응한다. Model은 '표시 형식에 의존하지 않는 내부 모델'을 조작하는 부분이다. 또 View는 Model이 '어떻게 보일 것인지'를 관리하고 있는 부분이다. 일반적으로 하나의 Model에 복수의 View가 대응한다.

## 5. 보강

### 1) java.util.Observer 인터페이스
Java의 클래스 라이브러리에 등장하는 java.util.Observer 인터페이스와 java.util.Observable 클래스는 Observer 패턴의 일종이다. java.util.Observer 인터페이스는 다음과 같은 메소드를 가지고 있다.

```java
public void update(Observable obj, Object art)
```

update 메소드에서는 인수로 관찰되는 대상(Subject 역할)으로서, Observable 클래스의 인스턴스, 부가 정보로서, Object 클래스의 인스턴스가 제공되고 있다.

'Java에는 Observer 패턴이 있으니까 이것을 사용하면 되겠구나'라고 생각할 수도 있지만, java.util.Observer 인터페이스와 java.util.Observable 클래스는 사용하기 쉽지 않다. 그 이유는 java.util.Observer 인터페이스에서 전달하는 Subject 역할에는 java.util.Observable 클래스(또는 그 하위 클래스)를 사용할 필요가 있기 때문이다. Java의 클래스는 단일 속성인 점을 기억해야 한다. Subject 역할로 만들려는 클래스(즉, 관찰대상이 되는 클래스)가 이미 무엇인가의 하위 클래스로 되어 있는 경우에는 java.util.Observable 클래스의 하위 클래스로 할 수 없다.

## 출처 및 참고
* Java 언어로 배우는 디자인 패턴 입문
* <https://stackoverflow.com/questions/46627299/observer-pattern-does-observable-needs-to-be-always-abstract>
