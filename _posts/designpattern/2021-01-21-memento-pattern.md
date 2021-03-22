---
title: Memento Pattern
author: dejavuhyo
date: 2021-01-21 11:30:00 +0900
categories: [Language, DesignPattern]
tags: [memento-pattern, memento, design-pattern, 메멘토-패턴, 디자인-패턴]
---

## 1. Memento 패턴이란
오브젝트 지향의 프로그램에서 undo 기능을 실행하려면 인스턴스가 가지고 있는 정보를 저장해 둘 필요가 있다. 단, 저장만 해서는 쓸모가 없고 저장한 정보로부터 인스턴스를 원래의 상태로 되돌려야 한다.

인스턴스를 복원하기 위해서는 인스턴스 내부의 정보를 자유롭게 액세스할 수 있어야 한다. 그러나 원하지 않는 액세스를 허용하면 클래스 내부 구조에 의존한 코드가 프로그램의 여기저기로 흩어져 클래스의 수정을 어렵게 만든다. 이것을 캡슐화의 파괴라고 한다. 인스턴스의 상태를 나타내는 역할을 도입해서 캡슐화의 파괴에 빠지지 않고 저장과 복원을 실행하는 것이 _**Memento 패턴**_ 이다.

Memento 패턴을 이용하면 프로그램에서 undo(실행취소), redo(재 실행), history(작업 이력의 작성), snapshot(현재 상태의 저장) 등을 실행할 수 있다. 어떤 시점의 인스턴스의 상태를 확실하게 기록해서 저장해 두면 나중에 인스턴스를 그 시점의 상태로 되돌릴 수 있다.

## 2. 객체

* Memento 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-21-memento-pattern/img001.png)

### 1) Originator(작성자)의 역할
자신의 현재 상태를 저장하고 싶을 때 Memento 역할을 만든다. 또한, Originator 역할은 이전의 Memento 역할을 전달받으면 그 Memento 역할을 만든 시점의 상태로 돌리는 처리를 실행한다.

### 2) Memento(기념품)의 역할
Originator 역할의 내부 정보를 정리한다. Memento 역할은 Originator 역할의 내부 정보를 가지고 있지만, 그 정보를 누구에게도 공개하지 않는다. Memento 역할은 두 종류의 인터페이스(API)를 가지고 있다.

> **wide interface:** Memento 역할이 제공하는 '넓은 인터페이스(API)'는 오브젝트의 상태를 원래의 상태로 돌리기 위해 필요한 정보를 모두 얻을 수 있는 메소드의 집합이다. 넓은 인터페이스(API)는 Memento 역할의 내부 상태를 속속들이 들어내기 때문에 이것을 사용하는 것은 Originator 역할뿐이다.

> **narrow interface:** Memento 역할이 제공하는 '좁은 인터페이스(API)'는 외부의 Caretaker 역할에게 보여주는 것이다. 좁은 인터페이스(API)로 할 수 있는 일에는 한계가 있고 내부 상태가 외부에 공개되는 것을 방지한다.

이 두 종류의 인터페이스(API)를 구별해서 사용하면 오브젝트의 캡슐화가 파괴되는 것을 방지할 수 있다.

### 3) Caretaker(관리인)의 역할
현재의 Originator 역할의 상태를 저장하고 싶을 때, 그것을 Originator 역할에게 전한다. Originator 역할은 그것을 받아서 Memento 역할을 만들어 Caretaker 역할에게 전달한다. Caretaker 역할은 미래의 필요에 대비해서 그 Memento 역할을 저장해 둔다.

그러나 Caretaker 역할은 Memento 역할이 갖는 2종류의 인터페이스(API) 중에서 '좁은 인터페이스(API)'만 사용할 수 있으므로 Memento 역할의 내부 정보에 액세스할 수 없다. 단, 만들어준 Memento 역할을 한 덩어리의 블랙박스로서 통째로 저장해 둘 뿐이다.

Originator 역할과 Memento 역할은 강하게 연결되어 있지만, Caretaker 역할과 Memento 역할은 유연하게 연결되어 있다. Memento 역할은 Caretaker 역할에 대해서 정보은폐를 수행하고 있다.

## 3. 예제

### 1) Memento 클래스
주인공(Gamer)의 상태를 표현하는 클래스이다. Memento 클래스와 Gamer 클래스는 공통의 패키지 game에 둔다.

Memento 클래스가 갖는 필드는 money와 fruits이다. money는 현재의 소지금, fruits는 현재 갖고 있는 과일이다. money와 fruits 필드가 private가 아닌 이유는 동일한 패키지 내의 Gamer 클래스로부터 필드에 자유롭게 액세스할 수 있도록 만들기 위해서이다. getMoney 메소드는 현재의 소지금을 얻는 메소드이다.

Memento 클래스의 생성자에는 public이 없다. 따라서 Memento 클래스의 인스턴스는 누구나 만들 수 있는 것이 아니고 동일한 패키지에 속해 있는 클래스에서만 사용할 수 있다. 구체적으로는 Memento 클래스의 인스턴스는 game 패키지의 Gamer 클래스가 생성한다.

addFruit 메소드는 과일을 추가하는 메소드이다. 이 메소드도 public이 아니다. 과일을 추가할 수 있는 것은 동일한 패키지뿐이다. 따라서, game 패키지의 외부에서는 Memento의 내부를 변경할 수 없다.

```java
package game;
import java.util.*;

public class Memento {
    int money; // 소지금
    ArrayList fruits; // 과일
    public int getMoney() { // 소지금을 얻는다(narrow interface)
        return money;
    }
    Memento(int money) { // 생성자(wide interface)
        this.money = money;
        this.fruits = new ArrayList();
    }
    void addFruit(String fruit) { // 과일을 추가한다(wide interface)
        fruits.add(fruit);
    }
    List getFruits() { // 과일을 얻는다(wide interface)
         return (List)fruits.clone();
    }
}
```

### 2) Gamer 클래스
게임을 실행하는 주인공을 표현하는 클래스이다. 소지금(money)과 과일(fruits)과 난수발생기(random)를 가지고 있다. 또한 클래스 필드로서 과일의 이름(fruitsname)을 가지고 있다.

게임의 중심이 되는 메소드는 bet(내기하다)이다. 이 메소드는 주인공이 파산하지 않는 한 주사위를 던져 나온 수에 따라 소지금이나 과일의 개수를 변화시킨다.

```java
package game;
import java.util.*;

public class Gamer {
    private int money; // 소지금
    private List fruits = new ArrayList(); // 과일
    private Random random = new Random(); // 난수발생기
    private static String[] fruitsname = { // 과일 이름의 표
        "사과", "포도", "바나나", "귤",
    };
    public Gamer(int money) { // 생성자
        this.money = money;
    }
    public int getMoney() { // 현재의 소지금을 얻는다
        return money;
    }
    public void bet() { // 내기하다 - 게임의 진행
        int dice = random.nextInt(6) + 1; // 주사위를 던지다
        if (dice == 1) { // 1 - 소지금이 증가한다
            money += 100;
            System.out.println("소지금이 증가했습니다.");
        } else if (dice == 2) { // 2 - 소지금이 절반이 된다
            money /= 2;
            System.out.println("소지금이 절반이 되었습니다.");
        } else if (dice == 6) { / 6 - 과일을 받는다
            String f = getFruit();
            System.out.println("과일(" + f + ")을 받았습니다.");
            fruits.add(f);
        } else { // 그외 - 변한 것이 없다
            System.out.println("변한 것이 없습니다.");
        }
    }
    public Memento createMemento() { // 스냅샷을 찍는다
        Memento m = new Memento(money);
        Iterator it = fruits.iterator();
        while (it.hasNext()) {
            String f = (String)it.next();
            if (f.startsWith("맛있는 ")) { // 과일은 맛있는 것만 보존
                m.addFruit(f);
            }
        }
        return m;
    }
    public void restoreMemento(Memento memento) { // undo 실행
        this.money = memento.money;
        this.fruits = memento.getFruits();
    }
    public String toString() { // 문자열 표현
        return "[money = " + money + ", fruits = " + fruits + "]";
    }
    private String getFruit() { // 과일을 1개 얻는다
        String prefix = "";
        if (random.nextBoolean()) {
            prefix = "맛있는 ";
        }
        return prefix + fruitsname[random.nextInt(fruitsname.length)];
    }
}
```

### 3) Main 클래스
Gamer의 인스턴스를 작성하고, 그것을 사용해서 게임을 실행한다. Gamer의 bet 메소드를 반복해서 호출하고 그때마다 소지금을 표시한다. 이것만으로는 단순한 주사위 게임에 지나지 않지만, 여기에서는 Memento 패턴을 도입하고 있다.

변수 memento에는 '어떤 시점의 Gamer의 상태'가 저장되어 있다. 운 좋게 소지금이 증가하면 createMemento를 사용해서 현재의 상태를 저장해 둔다.

소지금이 부족하게 되면 restoreMemento 메소드에게 이 memento를 제공해서 소지금을 원래 상태로 되돌린다.

```java
import game.Memento;
import game.Gamer;

public class Main {
    public static void main(String[] args) {
        Gamer gamer = new Gamer(100); // 최초의 소지금은 100
        Memento memento = gamer.createMemento(); // 최초의 상태를 저장해 둔다
        for (int i = 0; i < 100; i++) {
            System.out.println("==== " + i); // 개수 표시
            System.out.println("현상:" + gamer); // 현재의 주인공의 상태 표시

            gamer.bet();    // 게임을 진행시킨다

            System.out.println("소지금은" + gamer.getMoney() + "원이 되었습니다.");

            // Memento의 취급 결정
            if (gamer.getMoney() > memento.getMoney()) {
                System.out.println("    (많이 증가했으므로 현재의 상태를 저장하자)");
                memento = gamer.createMemento();
            } else if (gamer.getMoney() < memento.getMoney() / 2) {
                System.out.println("    (많이 감소했으므로 이전의 상태로 복원하자)");
                gamer.restoreMemento(memento);
            }

            // 시간 기다림
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
            }
            System.out.println("");
        }
    }
}
```

## 4. 개념 확장

### 1) 두 개의 인터페이스(API)와 액세스 제어
Memento 패턴에 등장하는 두 가지의 인터페이스(API)를 실현하기 위해서 예제 프로그램에서는 Java의 액세스 제어 기능을 사용했다.

* Java의 액세스 제어

| 액세스 제어 | 설명 |
|:---:|:---:|
| public | 모든 클래스에서 보인다 |
| protected | 그 패키지 및 하위 클래스에서 보인다 |
| 없음 | 그 패키지에서만 보인다 |
| private | 그 클래스에서만 보인다 |

Memento 클래스의 메소드나 필드에는 public이 붇어 있는 것과 아무 것도 없는 것 두 종류가 있다. 이것은 그 메소드나 필드를 어떤 클래스에게 보이고, 어떤 클래스에게는 보이지 않을 것인지를 프로그램 상에서 표현하기 위해서이다.

* Memento 클래스에서 사용되고 있는 액세스 제어

| 액세스 제어 | 필드 메소드 생성자 | 보이는 클래스 |
|:---:|:---:|:---:|
| 없음 | money | Memento 클래스, Gamer 클래스 |
| 없음 | fruits | Memento 클래스, Gamer 클래스 |
| public | getMoney | Memento 클래스, Gamer 클래스, Main 클래스 |
| 없음 | Memento | Memento 클래스, Gamer 클래스 |
| 없음 | addFruit | Memento 클래스, Gamer 클래스 |

Memento 클래스에서는 getMoney 메소드에게만 public을 붙여서 좁은 인터페이스(API)로 하고 있다. 이 메소드는 Caretaker 역할의 Main 클래스에서도 이용할 수 있다.

public이 붙어있는데 '좁다'는 것은 이상할 지 모른다. 여기에서 말하는 '좁다'는 '내부상태를 조작할 수 있는 정도가 적다'는 의미이다. Memento 클래스가 갖고 있는 메소드 중에서 public이 붙어있는 것은 getMoney 하나 뿐이다. 즉, 할 수 있는 일은 소지금을 얻는 일뿐이다. 이렇게 '할 수 있는 일이 적다'는 상태를 '좁다'라고 표현하고 있다.

Caretaker 역할의 Main 클래스는 game 패지에는 포함되어 있지 않기 때문에 Memento 클래스의 public인 메소드 getMoney밖에 사용할 수 없다. 따라서, 이 Main 클래스는 Memento 클래스의 내용을 멋데로 바꿀 수는 없다.

Main 클래스가 할 수 있는 일은 getMoney 메소드에서 소지금을 얻는 것과 Memento 클래스의 인스턴스를 변수 등에 저장해 두는 것뿐이다. Main 클래스에는 Memento의 생성자도 보이지 않는다. 생성자가 보이지 않는다는 것은 Main 클래스 안에서 Memento의 인스턴스를 만들 수 없다는 의미이다. Main 클래스는 Memento의 인스턴스가 필요할 때 createMemento 메소드를 호출해서 '현재의 상태를 저장하고 싶으니까 Memento의 인스턴스를 만들어 달라'고 Gamer 클래스에게 부탁한다.

이와 같이 액세스 제어를 이용하면 '이 클래스에서는 이 메소드를 보이지만, 저 클래스에서는 보이지 안흔다'라는 것을 프로그램에서 표현할 수 있다.

### 2) Memento의 갯수
Main 클래스가 배열 등을 사용해서 Memento의 인스턴스를 여러 개 갖도록 하면 인스턴스의 여러시점에서의 상태를 저장해 둘 수 있다.

### 3) Memento의 유효기간
Memento를 파일로서 지속적으로 저장할 경우에는 Memento의 '유효기간'이 문제가 된다.

어느 시점의 Memento를 파일로 저장해 두더라도 그 후 프로그램이 버전 업되면 파일로서 저장되어 있는 Memento와 모순이 발생할 우려가 있다.

### 4) Caretacker 역할과 Originator 역할을 분리하는 이유
Caretacker 역할은 어느 시점에서 스냅샷을 찍을지 결정하고, 언제 undo할지를 결정하는 Memento 역할을 저장한다. 한편, Originator 역할은 Memento 역할을 만드는 일과 제공된 Memento 역할을 사용해서 자신의 상태를 원래 상태로 돌리는 일을 수행한다. Caretacker 역할과 Originator 역할은 앞에서와 같이 역할분담을 수행하고 있다. 이와 같이 역할분담을 해두면 '여러 단계의 undo를 실행하도록 변경하고 싶다', 'undo 기능뿐만이 아니라 현재의 상태를 파일에 저장하고 싶다'라는 수정을 할 때에도 Originator 역할을 변경할 필요가 전혀 없다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://subscription.packtpub.com/book/application_development/9781786463593/3/ch03lvl1sec31/the-memento-pattern>
