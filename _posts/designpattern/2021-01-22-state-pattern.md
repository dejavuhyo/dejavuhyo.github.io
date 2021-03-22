---
title: State Pattern
author: dejavuhyo
date: 2021-01-22 11:00:00 +0900
categories: [Language, DesignPattern]
tags: [state-pattern, state, design-pattern, 상태-패턴, 디자인-패턴]
---

## 1. State 패턴이란
오브젝트 지향 프로그래밍에서는 프로그램 할 대상을 '클래스'로 표현한다. 어떤 것을 클래스로 표현할지는 설계를 하는 사람이 생각해야 한다. 클래스에 대응하는 구체적인 '사물'이 현실에서 존재하는 경우도 있고 존재하지 않는 경우도 있다. 경우에 따라서는 '이런 것이 클래스가 될 수 있나?'하고 놀랄 정도의 것을 클래스로 하는 경우도 있다.

State 패턴에서는 '상태'를 클래스로 표현한다. 현실세계에서 우리는 다양한 사물의 '상태'에 대해서 생각한다. 그러나 상태를 '사물'로 생각할 일이 별로 없기 때문에, 상태를 클래스로 표현한다는 것도 쉽게 이해가 안될 수도 있다.

상태를 클래스로 표현하면 클래스를 교체해서 '상태의 변화'를 표현할 수 있고, 새로운 상태를 추가해야 될 때 무엇을 프로그램하면 좋을지 분명해 진다. 이와 같은 디자인 패턴을 _**State 패턴**_ 이라고 한다.

## 2. 객체

* State 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-22-state-pattern/img001.png)

### 1) State(작성자)의 역할
상태를 나타낸다. 상태가 변할 때마다 다른 동작을 하는 인터페이스(API)를 결정한다. 이 인터페이스(API)는 상태에 의존한 동작을 하는 메소드의 집합이 된다.

### 2) ConcreteState(구체적인 상태)의 역할
구체적인 각각의 상태를 표현한다. State 역할로 결정되는 인터페이스(API)를 구체적으로 구현한다.

### 3) Context(상황, 전후관계, 문맥)의 역할
현재의 상태를 나타내는 ConcreteState 역할을 가진다. 또한, State 패턴의 이용자에게 필요한 인터페이스(API)를 결정한다.

## 3. 예제

### 1) State 인터페이스
금고의 상태를 나타낸다. State 인터페이스는 '시간이 설정되었을 때', '금고가 사용되었을 때', '비상벨이 눌렸을 때', '일반통화를 할 때'라는 사건에 대응해서 호출되는 인터페이스(API)를 규정하고 있다.

```java
public interface State {
    public abstract void doClock(Context context, int hour); // 시간설정
    public abstract void doUse(Context context); // 금고사용
    public abstract void doAlarm(Context context); // 비상벨
    public abstract void doPhone(Context context); // 일반통화
}
```

### 2) DayState 클래스
주간의 상태를 나타내는 클래스이다. 이 클래스는 State 인터페이스를 구현하고 있기 때문에 State 인터페이스에서 선언되어 있는 메소드를 구현하고 있다.

상태를 나타내는 클래스는 한 개씩 인스턴스를 만든다고 가정한다. 상태가 변화할 때마다 새로운 인스턴스를 만들게 되면, 메모리와 시간이 낭비되기 때문이다. 따라서 Singleton 패턴을 사용하고 있다.

```java
public class DayState implements State {
    private static DayState singleton = new DayState();
    private DayState() { // 생성자는 private
    }
    public static State getInstance() { // 유일한 인스턴스를 얻는다
        return singleton;
    }
    public void doClock(Context context, int hour) { // 시간설정
        if (hour < 9 || 17 <= hour) {
            context.changeState(NightState.getInstance());
        }
    }
    public void doUse(Context context) { // 금고사용
        context.recordLog("금고사용(주간)");
    }
    public void doAlarm(Context context) { // 비상벨
        context.callSecurityCenter("비상벨(주간)");
    }
    public void doPhone(Context context) { // 일반통화
        context.callSecurityCenter("일반통화(주간)");
    }
    public String toString() { // 문자열 표현
        return "[주간]";
    }
}
```

### 3) NightState 클래스
야간 상태를 나타내는 클래스이다. 이것도 DayState 클래스와 마찬가지로 Singleton 패턴을 사용하고 있다. 구성은 DayState 클래스와 동일하다.

```java
public class NightState implements State {
    private static NightState singleton = new NightState();
    private NightState() { // 생성자는 private
    }
    public static State getInstance() { // 유일한 인스턴스를 얻는다
        return singleton;
    }
    public void doClock(Context context, int hour) { // 시간설정
        if (9 <= hour && hour < 17) {
            context.changeState(DayState.getInstance());
        }
    }
    public void doUse(Context context) { // 금고사용
        context.callSecurityCenter("비상 : 야간금고 사용!");
    }
    public void doAlarm(Context context) { // 비상벨
        context.callSecurityCenter("비상벨(야간)");
    }
    public void doPhone(Context context) { // 일반통화
        context.recordLog("야간통화 녹음");
    }
    public String toString() { // 문자열 표현
        return "[야간]";
    }
}
```

### 4) Context 인터페이스
상태를 관리하거나 경비센터의 호출을 수행한다.

```java
public interface Context {

    public abstract void setClock(int hour); // 시간설정
    public abstract void changeState(State state); // 상태전환
    public abstract void callSecurityCenter(String msg); // 경비센터 호출
    public abstract void recordLog(String msg); // 경비센터 기록
}
```

### 5) SafeFrame 클래스
GUI를 사용해서 금고경비 시스템을 실현한다. SafeFrame 클래스는 Context 인터페이스를 구현한다.

```java
import java.awt.Frame;
import java.awt.Label;
import java.awt.Color;
import java.awt.Button;
import java.awt.TextField;
import java.awt.TextArea;
import java.awt.Panel;
import java.awt.BorderLayout;
import java.awt.event.ActionListener;
import java.awt.event.ActionEvent;

public class SafeFrame extends Frame implements ActionListener, Context {
    private TextField textClock = new TextField(60); // 현재시간 표시
    private TextArea textScreen = new TextArea(10, 60); // 경비센터 출력
    private Button buttonUse = new Button("금고사용"); // 금고사용 버튼
    private Button buttonAlarm = new Button("비상벨"); // 비상벨 버튼
    private Button buttonPhone = new Button("일반통화"); // 일반통화 버튼
    private Button buttonExit = new Button("종료"); // 종료 버튼

    private State state = DayState.getInstance(); // 현재의 상태

    // 생성자
    public SafeFrame(String title) {
        super(title);
        setBackground(Color.lightGray);
        setLayout(new BorderLayout());
        // textClock을 배치
        add(textClock, BorderLayout.NORTH);
        textClock.setEditable(false);
        // textScreen을 배치
        add(textScreen, BorderLayout.CENTER);
        textScreen.setEditable(false);
        // 패널에 버튼을 저장
        Panel panel = new Panel();
        panel.add(buttonUse);
        panel.add(buttonAlarm);
        panel.add(buttonPhone);
        panel.add(buttonExit);
        // 그 패널을 배치
        add(panel, BorderLayout.SOUTH);
        // 표시
        pack();
        show();
        // listener의 설정
        buttonUse.addActionListener(this);
        buttonAlarm.addActionListener(this);
        buttonPhone.addActionListener(this);
        buttonExit.addActionListener(this);
    }
    // 버튼이 눌려졌을 때 여기로 온다
    public void actionPerformed(ActionEvent e) {
        System.out.println(e.toString());
        if (e.getSource() == buttonUse) { // 금고사용 버튼
            state.doUse(this);
        } else if (e.getSource() == buttonAlarm) { // 비상벨 버튼
            state.doAlarm(this);
        } else if (e.getSource() == buttonPhone) { // 일반통화 버튼
            state.doPhone(this);
        } else if (e.getSource() == buttonExit) { // 종료 버튼
            System.exit(0);
        } else {
            System.out.println("?");
        }
    }
    // 시간설정
    public void setClock(int hour) {
        String clockstring = "현재 시간은";
        if (hour < 10) {
            clockstring += "0" + hour + ":00";
        } else {
            clockstring += hour + ":00";
        }
        System.out.println(clockstring);
        textClock.setText(clockstring);
        state.doClock(this, hour);
    }
    // 상태전환
    public void changeState(State state) {
        System.out.println(this.state + "에서" + state + "로 상태가 변화했습니다.");
        this.state = state;
    }
    // 경비센터의 호출
    public void callSecurityCenter(String msg) {
        textScreen.append("call! " + msg + "\n");
    }
    // 경비센터의 기록
    public void recordLog(String msg) {
        textScreen.append("record ... " + msg + "\n");
    }
}
```

### 6) Main 클래스
SafeFrame의 인스턴스를 한 개 만들어 그 인스턴스에 대해서 setClock으로 정기적으로 시간을 설정한다. 시간은 1초마다 설정되지만 프로그램 안에서는 1초가 1시간에 해당한다.

```java
public class Main {
    public static void main(String[] args) {
        SafeFrame frame = new SafeFrame("State Sample");
        while (true) {
            for (int hour = 0; hour < 24; hour++) {
                frame.setClock(hour); // 시간설정
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                }
            }
        }
    }
}
```

## 4. 개념 확장

### 1) 분할 통치
분할해서 통치해라(divide and conquer)하는 방침은 프로그래밍에 자주 등장한다. 이것은 복잡하고 규모가 큰 프로그램을 취급할 경우의 방침이다. 규모가 크고 복잡한 문제는 그대로 해결하려고 해서는 안된다. 우선 문제를 작은 문제로 나눈다. 그래도 해결하기 힘들면 더 작은 문제로 나눈다. 문제를 간단하게 해결할 때까지 작게 나누어서 해결한다. 크고 까다로운 문제를 하나 푸는 대신에 작고 쉬운 문제를 많이 풀어보아라. 분할해서 통치해라 하는 것은 간단하게 말하면 이런 것이다.

State 패턴에서는 '상태'를 클래스로 표현하였다. 각각의 구체적인 상태를 각각의 클래스로 표현해서 문제를 분할한 것이다. 하나의 ConcreteState 역할의 클래스를 코딩하고 있으면 프로그래머는 다른 클래스에 대한 생각을 잠시 잊을 수 있다. 예제 프로그램의 금고경비 시스템과 같이 상태가 두 가지밖에 없는 경우에는 별로 필요성을 느끼지 못하겠지만, 상태가 많을 때에는 State 패턴의 장점이 발휘된다.

State 패턴은 시스템의 '상태'를 클래스로 표현해서 복잡한 프로그램을 분할하고 있다.

### 2) 상태에 의존한 처리
SafeFrame 클래스의 setClock 메소드와 State 인터페이스의 doClock의 관계에 대해서 생각해 보자. SafeFrame 클래스의 setClock 메소드는 Main 클래스로부터 호출되고 있다. Main 클래스는 setClock 메소드를 호출해서 '시간의 설정'을 지시한다. setClock 메소드안에서는 그 처리를 state에 위임하고 있다. 즉, 시간의 설정을 '현재의 상태에 의존한 처리'로 취급하고 있다. 이것은 doClock 메소드에만 한정되지 않는다. State 인터페이스로 선언되고 있는 메소드는 모두 '상태에 의존한 처리'이고 '상태에 따라 동작이 달라지는 처리'이다.

State 패턴에서는 '상태에 의존한 처리'를 프로그램에서 '추상 메소드로서 선언하고 인터페이스로 한다', '구상 메소드로서 구현하고 각각의 클래스로 한다'의 두 가지 사항으로 정리할 수 있다. 이것이 State 패턴의 '상태에 의존한 처리'의 표현 방법이다.

### 3) 상태전환 관리
상태를 클래스로 표현해서 상태에 의존한 동작을 각각의 ConcreteState 역할에 분담시키는 방법은 매우 좋은 방법이다. 그러나 State 패턴을 사용할 경우 상태전환은 누가 관리해야 하는 지는 주의해야 한다.

예제 프로그램에서는 '상태전환'을 '상태에 의존한 동작'으로 간주하고 있다. 이 방법은 장점과 단점이 있다.

장점은 '다른 상태로 전환하는 것은 언제인가'하는 정보가 하나의 클래스 내에 정리되어 있다. 즉, 'DayState 클래스가 다른 상태로 전환하는 것은 언제인지' 알고 싶을 때에는 DayState 클래스의 코드를 읽으면 된다.

단점은 '하나의 ConcreteState 역할이 다른 ConcreteState 역할을 알아야 한다'는 점이다. 상태전환을 ConcreteState 역할에 맡기면 그 ConcreteState 역할이 적어도 다른 ConcreteState 역할을 알아야 한다. 즉, 상태전환을 ConcreteState 역할에 맡기면 클래스 사이의 의존관계를 깊게 한다.

### 4) 새로운 상태를 추가
State 패턴에 새로운 상태를 추가하는 것은 간단하다. 예제 프로그램처럼 State 인터페이스를 구현한 XXXState 클래스를 만들어 필요한 메소드를 구현하면 되기 때문이다. 단지, 상태전환의 부분은 다른 ConcreteState 혁할과의 접점이 되기 때문에 주의해서 코딩할 필요가 있다.

그리고 완성된 State 패턴에 새로운 '상태의존의 처리'를 추가하는 것은 곤란하다. 그것은 State 역할의 인터페이스에 메소드를 추가한다는 것을 의미하며, 모든 ConcreteState 역할에 처리를 추가하는 일이 되기 때문이다.

### 5) 두 가지 기능을 가진 인스턴스
SafeFrame 클래스 안에 등장한 'buttonUse.addActionListener(this);', 'state.doUse(this);' 이 두 개의 문에는 this가 있다. 둘 모두 SafeFrame 클래스의 인스턴스이다. 예제 프로그램에서 SafeFrame의 인스턴스는 한 개만 생성되므로 이 this는 같은 값이다.

그러나 addActionListener에 전달될 때와 doUse에 전달될 때에는 조금 차이가 있다.

addActionListener 메소드에 전달될 때, 이 인스턴스는 'ActionListener 인터페이스를 구현하고 있는 클래스의 인스턴스'로 취급된다. 이것은 addActionListener 메소드의 인수가 ActionListener형이기 때문이다. addActionListener 메소드 안에서는 'ActionListener 인터페이스에서 규정된 메소드의 범위'에서 인수가 이용된다. 인수로 전달된 것이 SafeFrame의 인스턴스인지 아닌지는 중요하지 않다.

doUse 메소드에 전달될 때, 동일한 인스턴스가 Context 인터페이스를 구현하고 있는 클래스의 인스턴스로 취급된다. 이것은 doUse 메소드의 인수가 Context 형이기 때문이다. doUse 메소드 안에서는 'Context 인터페이스에서 잔달된 메소드의 범위'에서 인수가 이용된다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://en.wikipedia.org/wiki/State_pattern>
