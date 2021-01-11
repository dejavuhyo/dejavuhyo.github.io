--- 
title: Strategy Pattern
author: Hyosik
date: 2021-01-11 11:40:00 +0900
categories: [Language, Java]
tags: [strategy-pattern, strategy, design-pattern, 전략-패턴, 디자인-패턴]
---

## 1. Strategy 패턴이란
모든 프로그램은 무제를 해결하기 위해 작성된다. 그리고 문제를 해결하기 위해 특정 알고리즘이 구현되고 있다. Strategy 패턴에서는 그 알고리즘을 구현한 부분을 모두 교환할 수 있다. 알고리즘(전략, 작전, 방책)을 빈틈없이 교체해서 같은 문제를 다른 방법으로도 해결할 수 있도록 도와주는 패턴이 _**Strategy 패턴**_ 이다.

## 2. 객체

* Strategy 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-11-strategy-pattern/img001.png)

### 1) Strategy(전략)의 역할
Strategy는 전략을 이용하기 위한 인터페이스(API)를 결정한다.

### 2) ConcreteStrategy(구체적인 전략)의 역할
ConcreteStrategy는 Strategy의 인터페이스(API)를 실제고 구현한다. 여기에서 구체적인 전략(작전, 방책, 방법, 알고리즘)을 실제로 프로그래밍한다.

### 3) Context(문맥)의 역할
Context는 Strategy를 이용하는 역할을 한다. ConcreteStrategy의 인스턴스를 가지고 있으며 필요에 따라 그것을 이용한다(Strategy의 인터페이스(API)를 호출한다).

## 3. 예제

* Hand 클래스
  - 클래스 내부에서 주먹은 0, 가위는 1, 보는 2라는 int로 표현하고 있다. 이것을 손의 값을 표시하는 필드(handvalue)에 저장한다. Hand 클래스의 인스턴스는 세 개만 작성되고, 처음에 세개의 인스턴스가 만들어져 배열 hand에 저장된다.
  - 클래스 메소드 getHand를 사용해서 인스턴스를 얻을 수 있다. 손의 값을 인수로 할당하면 인스턴스가 반환값이 된다.
  - 이 Hand 클래스는 다른 클래스(Player, WinningStrategy, ProbStrategy)로부터 사용되지만 Strategy 패턴의 역할에는 포함되지 않는다.

```java
public class Hand {
    public static final int HANDVALUE_GUU = 0; // 주먹을 표시하는 값
    public static final int HANDVALUE_CHO = 1; // 가위를 표시하는 값
    public static final int HANDVALUE_PAA = 2; // 보를 표시하는 값
    public static final Hand[] hand = { // 가위바위보의 손을 표시하는 3개의 인스턴스
        new Hand(HANDVALUE_GUU),
        new Hand(HANDVALUE_CHO),
        new Hand(HANDVALUE_PAA),
    };
    private static final String[] name = { // 가위바위보의 손의 문자열 표현
        "주먹", "가위", "보",
    };
    private int handvalue; // 가위바위보의 손의 값
    private Hand(int handvalue) {
        this.handvalue = handvalue;
    }
    public static Hand getHand(int handvalue) {  // 값에서 인스턴스를 얻는다
        return hand[handvalue];
    }
    public boolean isStrongerThan(Hand h) { // this가 h를 이길 경우 true
        return fight(h) == 1;
    }
    public boolean isWeakerThan(Hand h) { // this가 h에게 질 경우 true
        return fight(h) == -1;
    }
    private int fight(Hand h) { // 무승부는 0, this의 승이면 1, h의 승이면 -1
        if (this == h) {
            return 0;
        } else if ((this.handvalue + 1) % 3 == h.handvalue) {
            return 1;
        } else {
            return -1;
        }
    }
    public String toString() { // 문자열 표현으로 변환
        return name[handvalue];
    }
}
```

* Strategy 인터페이스
  - 가위바위보의 '전략'을 위한 추상 메소드의 집합이다. 이 메소드가 호출되면 Strategy 인터페이스를 구현하는 클래스는 전략적으로 '다음에 낼 손'을 결정한다.
  - Study는 '직전에 낸 손으로 이겼는지, 졌는지'를 학습하기 위한 메소드이다. 직전의 nextHand 메소드를 호출해서 이겼을 경우 study(true)로 호출하고, 진 경우 study(false)로 호출한다. 이에 따라 Strategy 인터페이스를 구현할 클래스는 자신의 내부 상태를 변화시켜 이후의 nexthand 메소드의 반환값을 결정하는 재료로 사용한다.

```java
public interface Strategy {
    public abstract Hand nextHand();
    public abstract void study(boolean win);
}
```

* WinningStrategy 클래스
  - Strategy 인터페이스를 구현하는 클래스 중 하나이다. Strategy 인터페이스를 구현한다는 것은 nextHand와 study라는 두 개의 메소드를 구현하는 것이 된다. 이 클래스는 직전 승부에서 이겼으면 다음에도 같은 손을 낸다는 어리석은 전략을 취한다. 만일 직전 승부에서 졌다면 다음 손은 난수를 사용해 결정한다.
  - random 필드는 이 클래스가 난수를 필요로 할 때 사용할 java.util.Random의 인스턴스를 저장한다. 말하자면 이 클래스가 사용할 난수발생기라고 할 수 있다.
  - won 필드는 이전 승부의 결과를 저장한다. 이겼으면 true, 졌으면 false가 된다.
  - prevHand 필드는 이전 승부에서 내밀었던 손을 저장한다.

```java
import java.util.Random;

public class WinningStrategy implements Strategy {
    private Random random;
    private boolean won = false;
    private Hand prevHand;
    public WinningStrategy(int seed) {
        random = new Random(seed);
    }
    public Hand nextHand() {
        if (!won) {
            prevHand = Hand.getHand(random.nextInt(3));
        }
        return prevHand;
    }
    public void study(boolean win) {
        won = win;
    }
}
```

* ProbStrategy 클래스
  - 또 하나의 구체적인 '전략'이다. 이것은 다음 손은 언제나 난수로 결정하지만, 과거 승패의 이력을 사용해서 각각의 손을 낼 확률을 바꾸고 있다.
  - history 필드는 과거의 승패를 반영한 확률계산을 위한 표를 만든다. history는 int의 2차원 배열로 각 차원의 첨자는 history [이전에 낸 손][이번에 낼 손]와 같은 의미를 가진다. 이 식의 값이 클수록 과거의 확률이 높다는 의미이다.
  - study 메소드는 nextHand 메소드에서 반환한 손의 승패를 기초로 history 필드의 내용을 갱신한다.

```java
import java.util.Random;

public class ProbStrategy implements Strategy {
    private Random random;
    private int prevHandValue = 0;
    private int currentHandValue = 0;
    private int[][] history = {
        { 1, 1, 1, },
        { 1, 1, 1, },
        { 1, 1, 1, },
    };
    public ProbStrategy(int seed) {
        random = new Random(seed);
    }
    public Hand nextHand() {
        int bet = random.nextInt(getSum(currentHandValue));
        int handvalue = 0;
        if (bet < history[currentHandValue][0]) {
            handvalue = 0;
        } else if (bet < history[currentHandValue][0] + history[currentHandValue][1]) {
            handvalue = 1;
        } else {
            handvalue = 2;
        }
        prevHandValue = currentHandValue;
        currentHandValue = handvalue;
        return Hand.getHand(handvalue);
    }
    private int getSum(int hv) {
        int sum = 0;
        for (int i = 0; i < 3; i++) {
            sum += history[hv][i];
        }
        return sum;
    }
    public void study(boolean win) {
        if (win) {
            history[prevHandValue][currentHandValue]++;
        } else {
            history[prevHandValue][(currentHandValue + 1) % 3]++;
            history[prevHandValue][(currentHandValue + 2) % 3]++;
        }
    }
}
```

* Player 클래스
  - 가위바위보를 하는 사람을 표현한 클래스이다. Player 클래스는 '이름'과 '전략'이 할당되어 인스턴스를 만든다. nextHand 메소드는 다음의 손을 위한 것이지만, 실제로 다음의 손을 결정하는 것은 자신의 '전략'이다. 전략의 nextHand 메소드의 반환값이 그대로 Player의 nextHand 메소드의 반환값이 된다. nextHand 메소드는 자신이 해야 할 처리를 Strategy에게 맡기고 있는 즉, '위임'을 하고 있다.
  - 이기거나(win), 지거나(lose), 비기거나(even) 한 승부의 결과를 다음 승부에 활용하기 위해서 Player 클래스는 strategy 필드를 통해서 study 메소드를 호출한다.
  - study 메소드를 사용해서 전략의 내부 상태를 변화시킨다. wincount, losecount, gamecount는 플레이어의 승부를 기록한다.

```java
public class Player {
    private String name;
    private Strategy strategy;
    private int wincount;
    private int losecount;
    private int gamecount;
    public Player(String name, Strategy strategy) { // 이름과 전략을 할당받는다
        this.name = name;
        this.strategy = strategy;
    }
    public Hand nextHand() { // 전략의 지시를 받는다
        return strategy.nextHand();
    }
    public void win() { // 승
        strategy.study(true);
        wincount++;
        gamecount++;
    }
    public void lose() { // 패
        strategy.study(false);
        losecount++;
        gamecount++;
    }
    public void even() { // 무승부
        gamecount++;
    }
    public String toString() {
        return "[" + name + ":" + gamecount + " games, " + wincount + " win, " + losecount + " lose" + "]";
    }
}
```

* Main 클래스
앞의 클래스를 이용해서 실제로 컴퓨터에서 가위바위보를 실행하기 위한 클래스이다. 여기에서는 두 명의 플레이어를 대전시키고 있다.

```java
public class Main {
    public static void main(String[] args) {
        if (args.length != 2) {
            System.out.println("Usage: java Main randomseed1 randomseed2");
            System.out.println("Example: java Main 314 15");
            System.exit(0);
        }
        int seed1 = Integer.parseInt(args[0]);
        int seed2 = Integer.parseInt(args[1]);
        Player player1 = new Player("두리", new WinningStrategy(seed1));
        Player player2 = new Player("하나", new ProbStrategy(seed2));   
        for (int i = 0; i < 10000; i++) {
            Hand nextHand1 = player1.nextHand();
            Hand nextHand2 = player2.nextHand();
            if (nextHand1.isStrongerThan(nextHand2)) {
                System.out.println("Winner:" + player1);
                player1.win();
                player2.lose();
            } else if (nextHand2.isStrongerThan(nextHand1)) {
                System.out.println("Winner:" + player2);
                player1.lose();
                player2.win();
            } else {
                System.out.println("Even...");
                player1.even();
                player2.even();
            }
        }
        System.out.println("Total result:");
        System.out.println(player1.toString());
        System.out.println(player2.toString());
    }
}
```

## 4. 개념 확장

### 1) Strategy 역할을 만드는 이유
보통 프로그래밍을 할 때 메소드 내부에 동화된 형태로 알고리즘을 구현하기 쉽다. 그러나 Strategy 패턴에서는 알고리즘의 부분을 다른 부분과 의식적으로 분리해서 알고리즘의 인터페이스(API) 부분만을 규정한다. 그리고 프로그램에서 위임에 의해 알고리즘을 이용한다.

이것은 프로그램을 복잡하게 만드는 것처럼 보이지만 실제로는 그렇지 않다. 예를 들어, 알고리즘을 개량해서 좀 더 빠르게 하고 싶다고 가정한다. Strategy 패턴을 사용하면 Strategy 역할의 인터페이스(API)를 변경하지 않도록 주의하고 ConcreteStrategy의 역할만을 수정하면 된다. 더욱이 위임이라는 느슨한 연결을 사용하고 있으므로 알고리즘을 용이하게 교환할 수 있다.

### 2) 실행 중에 교체도 가능
Strategy 패턴을 사용하면 프로그램의 동작 중에 concreteStrategy 역할의 클래스를 교체할 수도 있다. 예를 들어, 메모리가 적은 환경에서는 SlowButLessMemoryStrategy(속도는 느리지만 메모리를 절약하는 전략)을 사용하고, 메모리가 많은 환경에서는 FastButMoreMemoryStrategy(속도는 빠르지만 많이 사용하는 전략)을 사용하는 것도 생각할 수 있다.

한쪽의 알고리즘을 다른 쪽 알고리즘의 '검산'에 이용할 수도 있다. 예를 들어, 표 계산 소프트웨어의 디버그 판에서 복잡한 계산을 실행한다고 가정한다. 이때, '버그가 있을지도 모르는 고속의 알고리즘'과 '저속이지만 확실한 계산을 실행하는 알고리즘'을 준비해서 전자의 검산을 후자로 실행시키는 것이다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://www.researchgate.net/figure/The-structure-of-the-Strategy-pattern-It-provides-broadcast-communication-A-subject_fig3_257482232>
