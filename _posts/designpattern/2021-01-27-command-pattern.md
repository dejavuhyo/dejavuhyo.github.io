---
title: Command Pattern
author: dejavuhyo
date: 2021-01-27 10:40:00 +0900
categories: [Language, DesignPattern]
tags: [command-pattern, command, design-pattern, 커맨드-패턴, 디자인-패턴]
---

## 1. Command 패턴이란
클래스가 일을 실행할 때에는 자신의 클래스나 다른 클래스의 메소드를 호출한다. 메소드를 호출한 결과는 오브젝트(객체)의 상태에 반영되지만, 일의 이력은 어디에도 남지 않는다.

이때 '이 일을 실행하시오'라는 '명령'을 표현하는 클래스가 있으면 편리하다. 실행하고 싶은 일을 '메소드 호출'하는 동적인 처리로 표현하는 것이 아니라, 명령을 나타내는 클래스의 인스턴스로 하나의 '물건'처럼 표현할 수 있기 때문이다. 이력을 관리하고 싶을 때에는 그 인스턴스의 집합을 관리하면 된다. 명령의 집합을 저장해 두면 같은 명령을 재실행할 수도 있고, 또는 복수의 명령을 모아서 새로운 명령으로 재이용할 수도 있다.

디자인 패턴에서는 이와 같은 '명령'을 _**Command 패턴**_ 이라고 이름을 붙여 사용하고 있다.

Command는 Event라고도 부른다. '이벤트 구동 프로그래밍'에서 사용되는 '이벤트'와 같은 의미이다. 마우스를 클릭하거나 키를 누르는 등의 이벤트가 발생할 때, 그 사건을 일단 인스턴스라는 '물건'으로 해두고, 발생 순서에 따른 행렬로 나열한다. 그리고 나열해 놓은 이벤트를 순서대로 처리해 간다. GUI(graphic user interface)와 고ㅘㄴ련된 프로그래밍에서는 '이벤트'가 자주 등장한다.

## 2. 객체

* Command 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-27-command-pattern/img001.png)

### 1) Command(명령)의 역할
명령의 인터페이스(API)를 정의하는 역할이다.

### 2) ConcreteCommand(구체적 명령)의 역할
Command 역할의 인터페이스(API)를 실제로 구현하고 있는 역할이다.

### 3) Receiver(수신자)의 역할
Command 역할이 명령을 수행할 때 대상이 되는 역할이다. 명령을 받아들이는 수신자라고 불러도 될 것이다.

### 4) Client(의뢰자)의 역할
ConcreteCommand 역할을 생성하고, 그 사이에 Receiver 역할을 할당한다.

### 5) Invoker(기동자)의 역할
명령의 행동을 개시하는 역할이다. Command 역할에서 정의되는 인터페이스(API)를 호출하는 역할이 된다.

## 3. 예제

### 1) Command 인터페이스
'명령'을 표현하기 위한 인터페이스이고, 단 하나의 메소드 execute를 가진다. execute 메소드를 호출할 때 구체적으로 무슨 일이 일어날지는 Command 인터페이스를 구현한 클래스가 결정하지만, 어쨋든 무언가를 '실행한다'는 것이 Command 인터페이스이다.

```java
package command;

public interface Command {
    public abstract void execute();
}
```

### 2) MacroCommand 클래스
'복수의 명령을 모은 명령'을 나타낸다. 이 클래스는 Command 인터페이스를 구현하고 있다.

MacroCommand 클래스는 Command 인터페이스를 구현하고 있기 때문에 execute 메소드가 정의되어 있다. execute 메소드는 복수의 명령을 실행하기 위해서는 commands 필드에 저장되어 있는 각각의 인스턴스의 execute 메소드를 호출한다. 이렇게 하면 자신이 저장하고 있는 복수의 Command를 모두 실행한 결과가 된다. 어쩌면 이 while 루프 안에서 실행하려고 하는 Command는 또 다른 MacroCommand의 인스턴스일지도 모른다. 이 경우에도 다시 그 인스턴스의 execute가 호출되어 결국 모든 Command가 실행된다.

```java
package command;

import java.util.Stack;
import java.util.Iterator;

public class MacroCommand implements Command {
    // 명령의 집합
    private Stack commands = new Stack();
    // 실행
    public void execute() {
        Iterator it = commands.iterator();
        while (it.hasNext()) {
            ((Command)it.next()).execute();
        }
    }
    // 추가
    public void append(Command cmd) {
        if (cmd != this) {
            commands.push(cmd);
        }
    }
    // 최후의 명령을 삭제
    public void undo() {
        if (!commands.empty()) {
            commands.pop();
        }
    }
    // 전부 삭제
    public void clear() {
        commands.clear();
    }
}
```

### 3) DrawCommand 클래스
Command 인터페이스를 구현한 클래스이고, '점 그리기 명령'을 표현한 것이다. drawable과 position이라는 두 개의 필드를 가지고 있다. drawable 필드는 그리기를 실행하는 대상을 저장한다. position 필드는 그리기를 실행하는 위치를 나타낸다. Point 클래스는 java.awt 패키지에서 정해져 있는 클래스로, X좌표화 Y좌표를 가진 이차원 평면의 위치를 나타낸다.

```java
package drawer;

import command.Command;
import java.awt.Point;

public class DrawCommand implements Command {
    // 그림 그리기 대상
    protected Drawable drawable;
    // 그림 그리기 위치
    private Point position;
    // 생성자
    public DrawCommand(Drawable drawable, Point position) {
        this.drawable = drawable;
        this.position = position;
    }
    // 실행
    public void execute() {
        drawable.draw(position.x, position.y); 
    }
}
```

### 4) Drawable 인터페이스
'그림 그리기 대상'을 표현하는 것이다. draw는 그림 그리기를 하는 메소드이다.

```java
package drawer;

public interface Drawable {
    public abstract void draw(int x, int y);
}
```

### 5) DrawCanvas 클래스
Drawable 인터페이스를 구현하는 클래스이고 java.awt.Canvas 클래스의 하위 클래스이다. 그림 그리기 명령의 집함은 history 필드에 저장된다. 이 필드의 형은 command.MacroCommand이다.

```java
package drawer;

import command.*;

import java.util.*;
import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

public class DrawCanvas extends Canvas implements Drawable {
    // 그림 그리는 색
    private Color color = Color.red;
    // 그림 그리는 점의 반경
    private int radius = 6;
    // 이력
    private MacroCommand history;
    // 생성자
    public DrawCanvas(int width, int height, MacroCommand history) {
        setSize(width, height);
        setBackground(Color.white);
        this.history = history;
    }
    // 이력 전체를 다시 그리기
    public void paint(Graphics g) {
        history.execute();
    }
    // 그림 그리기
    public void draw(int x, int y) {
        Graphics g = getGraphics();
        g.setColor(color);
        g.fillOval(x - radius, y - radius, radius * 2, radius * 2);
    }
}
```

### 6) Main 클래스
예제 프로그램을 동작시키기 위한 것이다.

```java
import command.*;
import drawer.*;

import java.awt.*;
import java.awt.event.*;
import javax.swing.*;

public class Main extends JFrame implements ActionListener, MouseMotionListener, WindowListener {
    // 그림 그린 이력
    private MacroCommand history = new MacroCommand();
    // 그림 그리는 영역
    private DrawCanvas canvas = new DrawCanvas(400, 400, history);
    // 제거 버튼
    private JButton clearButton  = new JButton("clear");

    // 생성자
    public Main(String title) {
        super(title);

        this.addWindowListener(this);
        canvas.addMouseMotionListener(this);
        clearButton.addActionListener(this);

        Box buttonBox = new Box(BoxLayout.X_AXIS);
        buttonBox.add(clearButton);
        Box mainBox = new Box(BoxLayout.Y_AXIS);
        mainBox.add(buttonBox);
        mainBox.add(canvas);
        getContentPane().add(mainBox);

        pack();
        show();
    }

    // ActionListener용
    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == clearButton) {
            history.clear();
            canvas.repaint();
        }
    }

    // MouseMotionListener용
    public void mouseMoved(MouseEvent e) {
    }
    public void mouseDragged(MouseEvent e) {
        Command cmd = new DrawCommand(canvas, e.getPoint());
        history.append(cmd);
        cmd.execute();
    }

    // WindowListener용
    public void windowClosing(WindowEvent e) {
        System.exit(0);
    }
    public void windowActivated(WindowEvent e) {}
    public void windowClosed(WindowEvent e) {}
    public void windowDeactivated(WindowEvent e) {}
    public void windowDeiconified(WindowEvent e) {}
    public void windowIconified(WindowEvent e) {}
    public void windowOpened(WindowEvent e) {}

    public static void main(String[] args) {
        new Main("Command Pattern Sample");
    }
}
```

## 4. 개념 확장

### 1) 명령이 가지고 있어야 할 정보
'명령'에 어느 정도의 정보를 갖게 할지는 목적에 따라서 다르다. DrawCommand 클래스에는 그림을 그리는 점의 위치라는 정보만을 가지게 했다. 점의 크기나 색, 형태 등의 정보는 가지고 있지 않는다.

DrawCommand가 '이벤트가 발생한 시간(타임 스탬프)'이라는 정보를 가지고 있다고 가정하면, 다시 그림을 그릴 때 단지 그림을 그리는 것만이 아니라 사용자의 마우스 동작의 완급을 재현할 수 있을지도 모른다.

그런데 DrawCommand 클래스는 그림 그리는 대상을 나타내는 필드(drawable)도 가지고 있다. 예제 프로그램에서는 DrawCommand의 인스턴스는 하나뿐이고, 모든 그림 그리기는 여기에 대응해서 실행되기 때문에 이 drawable 필드는 별로 의미가 없다. 그러나 그림 그리기의 대상(죽, Receiver 역할)이 여러 개 존재하는 프로그램의 경우에는 이와 같은 필드가 도움이 된다. ConcreteCommand 역할 자신이 Receiver 역할을 '알고 있기' 때문에, ConcreteCommand 역할을 누가 관리하고 누가 가지고 있어도 execute할 수 있다.

### 2) 이력의 저장
예제 프로그램에서는 그림 그리기의 이력을 MacroCommand의 인스턴스(history)로 표현했다. 이 인스턴스는 지금까지의 그림 그리기의 모든 정보를 가지고 있다. 이것은 이 인스턴스를 파일로 잘 저장해 두면 그림 그리기의 이력이 저장된다는 의미이다.

### 3) 어댑터
프로그래밍을 간결하게 하기 위해 어댑터라는 클래스들이 java.awt.event 패키지에 준비되어 있다. 예를 들어, MouseMotionListener 인터페이스에 대해서는 MouseMotionAdapter 클래스, WindowListener 인터페이스에 대해서는 WindowAdapter 클래스가 준비되어 있다. 이와 같은 어댑터는 Adapter 패턴의 일례이다.

* 인터페이스와 어댑터

| 인터페이스 | 어탭터 |
|-----|-----|
| MouseMotionListener 인터페이스 | MouseMotionAdapter 클래스 |
| WindowListener 인터페이스 | WindowAdapter 클래스 |

MouseMotionListener 클래스를 예로 들면, 이 클래스는 MouseMotionListener 인터페이스를 구현하고 이 인터페이스가 요구하는 메소드를 모두 구현하고 있다. 그러나 이 구현은 모두 거짓(아무것도 하지 않는 메소드)으로 되어 있다. 따라서 MouseMotionListener 클래스의 하위 클래스를 만들어 필요한 메소드만을 구현해서 목적을 달성할 수 있다.

특히, Java의 익명 내부 클래스(anonymous inner class)라는 기구와 조합해서 어탭터를 사용하면 더욱 깔끔한 프로그램을 기술할 수 있다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://itnext.io/easy-patterns-command-15733a2b56f0>
