---
title: Mediator Pattern
author: dejavuhyo
date: 2021-01-19 11:10:00 +0900
categories: [Language, DesignPattern]
tags: [mediator-pattern, mediator, design-pattern, 중재자-패턴, 디자인-패턴]
---

## 1. Mediator 패턴이란
mediator는 '조정자', '중개자'라는 의미이지만, 의지할 수 있는 '중개인'을 상상하는 편이 이해하기 쉬울 것이다. 곤란한 일이 생기거나 모임 전체에 영향을 미칠만한 일이 발생하면 중개인에게 알리고, 중개인의 지시대로 실행한다. 모임의 각 회원이 멋대로 다른 회원과 대화해서 판단하는 것이 아니라, 언제나 중개인을 통해서 행동을 하도록 한다. 한편, 중개인은 모임의 회원으로부터 올라온 보고를 기초로 대국적인 판단을 해서 각 회원에게 지시를 내린다. 이와 같은 디자인 패턴을 _**Mediator 패턴**_ 이라고 한다.

## 2. 객체

* Mediator 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-19-mediator-pattern/img001.png)

### 1) Mediator(조정자)의 역할
Colleague 역할과 통신을 해서 조정을 실행하기 위한 인터페이스(API)를 결정한다.

### 2) ConcreteMediator(구체적인 조정자, 중개자)의 역할
Mediator 역할의 인터페이스(API)를 구현해서 실제의 조정을 실행한다.

### 3) Colleague(동료)의 역할
Mediator 역할과 통신을 실행할 인터페이스(API)를 구현한다.

### 4) ConcreteColleague(구체적인 동료)의 역할
Colleague 역할의 인터페이스(API)를 구현한다.

## 3. 예제

### 1) Mediator 인터페이스
'중개인'을 표현하는 인터페이스이다. 구체적인 중개인은 이 인터페이스를 구현한다. createColleagues 메소드는 Mediator가 관리하는 회원을 생성하는 메소드이다.

colleagueChanged 메소드는 각 회원인 Colleague들에서 호출되는 메소드이다. 이 메소드는 중개인에 대한 '상담'에 해당한다.

```java
public interface Mediator {
    public abstract void createColleagues();
    public abstract void colleagueChanged();
}
```

### 2) Colleague 인터페이스
중개인에게 상담을 의뢰하는 회원을 나타내는 인터페이스이다. 구체적인 회원(ColleagueButton, ColleagueTextField, ColleagueCheckbox)은 이 인터페이스를 구현한다.

```java
public interface Colleague {
    public abstract void setMediator(Mediator mediator);
    public abstract void setColleagueEnabled(boolean enabled);
}
```

### 3) ColleagueButton 클래스
java.awt.Button으ㅟ 하위 클래스이지만, Colleague 인터페이스를 구현해서 LogFrame 클래스(Mediator 인터페이스)와 협조 동작을 수행한다.

```java
import java.awt.Button;

public class ColleagueButton extends Button implements Colleague {
    private Mediator mediator;
    public ColleagueButton(String caption) {
        super(caption);
    }
    public void setMediator(Mediator mediator) { // Mediator을 저장
        this.mediator = mediator;
    }
    public void setColleagueEnabled(boolean enabled) { // Mediator에서 유효/무효를 지시
        setEnabled(enabled);
    }
}
```

### 4) ColleagueTextField 클래스
java.awt.TextField의 하위 클래스로 Colleague 인터페이스를 구현하고 있다. 또한, 이 클래스는 java.awt.event.TextListener 인터페이스도 구현하고 있다. 이것은 텍스트의 내용이 변했을 때 textValueChanged 메소드에서 캐치하기 위해서이다.

Java의 클래스는 복수의 클래스에서 확장(extends)할 수 없지만, 복수의 인터페이스를 구현(implements)할 수는 있다.

```java
import java.awt.TextField;
import java.awt.Color;
import java.awt.event.TextListener;
import java.awt.event.TextEvent;

public class ColleagueTextField extends TextField implements TextListener, Colleague {
    private Mediator mediator;
    public ColleagueTextField(String text, int columns) { // 생성자
        super(text, columns);
    }
    public void setMediator(Mediator mediator) { // Mediator을 저장
        this.mediator = mediator;
    }
    public void setColleagueEnabled(boolean enabled) { // Mediator에서 유효/무효를 지시
        setEnabled(enabled);
        setBackground(enabled ? Color.white : Color.lightGray);
    }
    public void textValueChanged(TextEvent e) { // 문자열이 변하면 Mediator에게 통지
        mediator.colleagueChanged();
    }
}
```

### 5) ColleagueCheckbox 클래스
java.awt.Checkbox 클래스의 하위 클래스이다. 이 클래스는 java.awt.event.ItemListener 인터페이스도 구현하고 있다. 이것은 라이도 버튼의 상태 변화를 itemStateChanged 메소드에서 캐치하고 싶기 때문이다.

```java
import java.awt.Checkbox;
import java.awt.CheckboxGroup;
import java.awt.event.ItemListener;
import java.awt.event.ItemEvent;

public class ColleagueCheckbox extends Checkbox implements ItemListener, Colleague {
    private Mediator mediator;
    public ColleagueCheckbox(String caption, CheckboxGroup group, boolean state) {  
// 생성자
        super(caption, group, state);
    }
    public void setMediator(Mediator mediator) { // Mediator을 저장
        this.mediator = mediator;
    }
    public void setColleagueEnabled(boolean enabled) { // Mediator에서 유효/무효를 지시
        setEnabled(enabled);
    }
    public void itemStateChanged(ItemEvent e) { // 상태가 바뀌면 Mediator에게 통지
        mediator.colleagueChanged();
    }
}
```

### 6) LoginFrame 클래스
java.awt.Frame(GUI 어플리케이션을 만들기 위한 클래스)의 하위 클래스로 Mediator 인터페이스를 구현하고 있다.

이 프로그램에서 가장 중요한 메소드는 LoginFrame 클래스의 colleagueChanged 메소드이다. 이 메소드 안에서 '표시의 유효/무효를 설정하는 복잡한 처리'를 행하고 있다. ColleagueButton, ColleagueTextField, ColleagueCheckbox의 각 클래스에게는 유효/무효를 설정하는 메소드는 있었지만, 어느 경우에 유효/무효로 할지에 관한 로직은 써있지 않았다. 모든 클래스가 colleagueChanged 메소드를 호출하기만 하면 된다. 즉, 모든 Colleague의 상담이 여기의 colleagueChanged 메소드로 집결한다.

나머지는 라디오 버튼의 상태를 얻는 getState 메소드나 텍스트 필드의 문자열을 얻는 getText 메소드 등을 구사해서 복잡한 조건을 코딩하면 된다. 또한, 여기에서는 공통의 처리를 위해 userpassChanged 메소드를 호출하고 있다. userpassChanged 메소드는 내부에서만 사용되고 있는 private한 메소드이다.

```java
import java.awt.Frame;
import java.awt.Label;
import java.awt.Color;
import java.awt.CheckboxGroup;
import java.awt.GridLayout;
import java.awt.event.ActionListener;
import java.awt.event.ActionEvent;

public class LoginFrame extends Frame implements ActionListener, Mediator {
    private ColleagueCheckbox checkGuest;
    private ColleagueCheckbox checkLogin;
    private ColleagueTextField textUser;
    private ColleagueTextField textPass;
    private ColleagueButton buttonOk;
    private ColleagueButton buttonCancel;

    // 생성자
    // Colleague들을 생성하고, 배치한 후에 표시를 실행한다
    public LoginFrame(String title) {
        super(title);
        setBackground(Color.lightGray);
        // 레이아웃 매니저를 사용해서 4X2의 그리드를 만든다
        setLayout(new GridLayout(4, 2));
        // Colleague들의 생성
        createColleagues();
        // 배치
        add(checkGuest);
        add(checkLogin);
        add(new Label("Username:"));
        add(textUser);
        add(new Label("Password:"));
        add(textPass);
        add(buttonOk);
        add(buttonCancel);
        // 유효/무효의 초기 지정
        colleagueChanged();
        // 표시
        pack();
        show();
    }

    // Colleague들을 생성한다
    public void createColleagues() {
        // 생성
        CheckboxGroup g = new CheckboxGroup();
        checkGuest = new ColleagueCheckbox("Guest", g, true);
        checkLogin = new ColleagueCheckbox("Login", g, false);
        textUser = new ColleagueTextField("", 10);
        textPass = new ColleagueTextField("", 10);
        textPass.setEchoChar('*');
        buttonOk = new ColleagueButton("OK");
        buttonCancel = new ColleagueButton("Cancel");
        // Mediator의 세트
        checkGuest.setMediator(this);
        checkLogin.setMediator(this);
        textUser.setMediator(this);
        textPass.setMediator(this);
        buttonOk.setMediator(this);
        buttonCancel.setMediator(this);
        // Listener의 세트
        checkGuest.addItemListener(checkGuest);
        checkLogin.addItemListener(checkLogin);
        textUser.addTextListener(textUser);
        textPass.addTextListener(textPass);
        buttonOk.addActionListener(this);
        buttonCancel.addActionListener(this);
    }

    // Colleage에서의 통지로 Colleage의 유효/무효를 판정한다
    public void colleagueChanged() {
        if (checkGuest.getState()) { // Guest 모드
            textUser.setColleagueEnabled(false);
            textPass.setColleagueEnabled(false);
            buttonOk.setColleagueEnabled(true);
        } else { // Login 모드
            textUser.setColleagueEnabled(true);
            userpassChanged();
        }
    }
    // textUser 또는 textPass의 변경이 있었다
    // 각 Colleage의 유효/무효를 판정한다
    private void userpassChanged() {
        if (textUser.getText().length() > 0) {
            textPass.setColleagueEnabled(true);
            if (textPass.getText().length() > 0) {
                buttonOk.setColleagueEnabled(true);
            } else {
                buttonOk.setColleagueEnabled(false);
            }
        } else {
            textPass.setColleagueEnabled(false);
            buttonOk.setColleagueEnabled(false);
        }
    }
    public void actionPerformed(ActionEvent e) {
        System.out.println(e.toString());
        System.exit(0);
    }
}
```

### 7) Main 클래스
LoginFrame의 인스턴스를 생성하고 있다. main 메소드는 종료하지만, LoginFrame의 인스턴스는 AWT의 framework 쪽에 저장되어 있다.

```java
import java.awt.*;
import java.awt.event.*;

public class Main {
    static public void main(String args[]) {
        new LoginFrame("Mediator Sample");
    }
}
```

## 4. 개념 확장

### 1) 분산이 화를 부를 때
예제 프로그램의 LoginFrame에 기술되어 있는 colleagueChanged 메소드는 다소 복잡하다. 사양이 변경되면 이 복잡한 메소드 내부에 결국 버그가 발생할 경우도 있지만 그것은 문제가 되지 않는다. 가령 colleagueChanged 메소드에 버가그 발생해도 표시의 유효/무효에 관한 로직은 여기 외에는 존재하지 않기 때문에 여기를 디버그하면 된다. 만약, 로직이 ColleagueButton, ColleagueTextField, ColleagueCheckbox에 분산되어 있으면 기술하는 것과 디버그하는 것도 수정하는 것도 큰 문제일 것이다.

오브젝트(객체) 지향에서는 한 곳에 집중되는 것을 피해서 처리를 분산시키는 경우가 많다. 즉, 문제를 '분할해서 통치'하려는 것이다. 그러나 예제 프로그램과 같은 경우에는 처리를 각 클래스에 분산시키는 것은 현명하지 못하다. 각 클래스에 분산시킬 것은 분산시키고, 집중시킬 것은 집중시키지 않으면 모처럼의 클래스 분산이 오히려 화를 부르게 된다.

### 2) 통신 경로의 증가
A와 B, 2개의 인스턴스가 있고 서로 통신(메소드를 서로 호출)한다고 가정하자. 이때 통신 경로는 A→B와 A←B로 2가지가 된다. 인스턴스가 A와 B와 C로 3개라면 A→B, A←B, B→C, B←C, C→A, C←A 6가지가 된다. 인스턴스가 4개이면 12가지로 되고, 5개이면 20가지가 되고, 6개이면 30가지가 된다. 같은 입장의 인스턴스가 많이 존재할 때 그것들을 서로 통신시키면 프로그램은 복잡해 진다.

인스턴스의 수가 적을 때에는 별로 문제가 안되지만, 최초의 설계대로 점점 인스턴스를 증가시키면 언젠가는 파국을 초래할 것이다.

### 3) 재이용할 수 있는 것
ConcreteColleague 역할은 재이용하기 쉽지만 ConcreteMediator 역할은 재이용하기 어렵다. 예를 들어, 로그인 다이얼로그와는 별개로 새로운 다이얼로그를 만든다고 가정한다. 이때, ConcreteColleague 역할인 ColleagueButton, ColleagueTextField, ColleagueCheckbox는 새로운 다이얼로그에서도 재이용할 수 있다. 왜냐면 ConcreteColleague 역할 안에는 특정한 다이얼로그에 의존하는 코드가 없기 때문이다.

코드 가운데 어플리케이션에 대한 의존성이 높은 부분은 ConcreteMediator 역할인 LoginFrame 클래스 안에 갇혀 있다. 어플리케이션에 대한 의존도가 높다는 것은 재이용성이 낮다는 것이다. LoginFrame 클래스를 그대로 다른 다이얼로그에 재이용하는 것은 어렵다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* [https://mertarauh.com/tutorials/typescript-design-patterns/mediator-pattern/](https://mertarauh.com/tutorials/typescript-design-patterns/mediator-pattern/)
