---
title: Flyweight Pattern
author: Hyosik
date: 2021-01-25 11:20:00 +0900
categories: [Language, Java]
tags: [flyweight-pattern, flyweight, design-pattern, 플라이웨이트-패턴, 디자인-패턴]
---

## 1. Flyweight 패턴이란
Flyweight 디자인 패턴은 오브젝트(객체)를 '가볍게'하기 위한 것이다. 오브젝트는 컴퓨터 안에서 가상적으로 존재하는 것이기 때문에 '무겁다' 혹은 '가볍다'라고 표현해도 실제의 무게를 나타내는 것은 아니다. 여기에서 말하는 무게는 '메모리의 사용량'을 의미한다. 많은 메모리를 사요하는 오브젝트를 '무겁다'고 표현하고, 적은 메모리를 사용하는 오브젝트를 '가볍다'라고 표현하고 있다.

Flyweight 패턴에서 사용하는 기법은 '인스턴스를 가능한 대로 공유시켜서 쓸데없이 new하지 않도록 한다'라는 것이다. 인스턴스가 필요할 때 항상 new하는 것이 아니라, 이미 만들어져 있는 인스턴스를 이용할 수 있으면 그것을 공유해서 사용한다. 이것이 _**Flyweight 패턴**_ 의 핵심이다.

## 2. 객체

* Flyweight 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-25-flyweight-pattern/img001.png)

### 1) Flyweight(플라이급)의 역할
평소대로 취급하면 프로그램이 무거워지기 때문에 공유하는 것이 좋은 것을 나타내는 역할이다.

### 2) FlyweightFactory(플라이급의 공장)의 역할
FlyweightFactory 역할을 만드는 공장의 역할이다. 이 공장을 사용해서 Flyweight 역할을 만들면 인스턴스가 공유된다.

### 3) Client(의뢰자)의 역할
FlyweightFactory 역할을 사용해서 Flyweight 역할을 만들고 그것을 이용하는 역할이다.

## 3. 예제

### 1) BigChar 클래스
'큰 문자'를 나타내는 클래스이다. 생성자에서는 인수로 주어진 문자의 '큰 문자' 버전을 작성한다. 작성한 문자열은 fontdata 필드에 저장된다.

이 클래스에는 Flyweight 패턴의 '공유'에 관련된 코드는 아직 등장하지 않는다. 공류를 제어하고 있는 것은 리스트의 BigCharFactory 클래스이다.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class BigChar {
    // 문자의 이름
    private char charname;
    // 큰 문자를 표현하는 문자열 ('#' '.' '\n'의 열)
    private String fontdata;
    // 생성자
    public BigChar(char charname) {
        this.charname = charname;
        try {
            BufferedReader reader = new BufferedReader(
                new FileReader("big" + charname + ".txt")
            );
            String line;
            StringBuffer buf = new StringBuffer();
            while ((line = reader.readLine()) != null) {
                buf.append(line);
                buf.append("\n");
            }
            reader.close();
            this.fontdata = buf.toString();
        } catch (IOException e) {
            this.fontdata = charname + "?";
        }
    }
    // 큰 문자를 표현한다
    public void print() {
        System.out.print(fontdata);
    }
}
```

### 2) BigCharFactory 클래스
BigChar의 인스턴스를 생성하는 공장(Factory)이다. 여기에서 공유의 방법을 실현하고 있다.

pool 필드는 지금까지 만든 BigChar의 인스턴스를 관리한다. 여기에서 사용되고 있는 java.util.HashMap은 '문자열 → 인스턴스'라는 대응관계를 관리하기 위한 클래스이다. java.util.HashMap 클래스의 put 메소드에서 어떤 문자열(키)에 대해서 하나의 인스턴스(값)를 대응시킨다. 그리고 get 메소드에서 키에 대응하는 값을 얻을 수 있다.

BigCharFactory 클래스는 Singleton 패턴을 사용해서 구현되고 있다. BigCharFactory의 인스턴스는 하나만 존재하면 되기 때문이다. getInstance 메소드는 BigCharFactory의 인스턴스(BigChar의 인스턴스가 아니다)를 얻기 위한 메소드 이다.

```java
import java.util.HashMap;

public class BigCharFactory {
    // 이미 만들어진 BigChar의 인스턴스를 관리
    private HashMap pool = new HashMap();
    // Singleton 패턴
    private static BigCharFactory singleton = new BigCharFactory();
    // 생성자
    private BigCharFactory() {
    }
    // 유일한 인스턴스를 얻는다
    public static BigCharFactory getInstance() {
        return singleton;
    }
    // BigChar의 인스턴스 생성(공유)
    public synchronized BigChar getBigChar(char charname) {
        BigChar bc = (BigChar)pool.get("" + charname);
        if (bc == null) {
            bc = new BigChar(charname); // 여기에서 BigChar의 인스턴스를 생성
            pool.put("" + charname, bc);
        }
        return bc;
    }
}
```

### 3) BigString 클래스
BigChar를 모은 '큰 문자열' 클래스이다. bigchars 필드는 BigChar의 배열이고, BigChar의 인스턴스를 저장한다.

```java
public class BigString {
    // 「큰 문자」
    private BigChar[] bigchars;
    // 생성자
    public BigString(String string) {
        bigchars = new BigChar[string.length()];
        BigCharFactory factory = BigCharFactory.getInstance();
        for (int i = 0; i < bigchars.length; i++) {
            bigchars[i] = factory.getBigChar(string.charAt(i));
        }
    }
    // 표시
    public void print() {
        for (int i = 0; i < bigchars.length; i++) {
            bigchars[i].print();
        }
    }
}
```

### 4) Main 클래스
인수로 주어진 문자열을 기본으로 BigString의 인스턴스를 만들고 그것을 표시한다.

```java
public class Main {
    public static void main(String[] args) {
        if (args.length == 0) {
            System.out.println("Usage: java Main digits");
            System.out.println("Example: java Main 1212123");
            System.exit(0);
        }
        BigString bs = new BigString(args[0]);
        bs.print();
    }
}
```

## 4. 개념 확장

### 1) 여러 장소에 영향을 미친다
Flyweight 패턴에서는 인스턴스를 공유하는 것이 테마이다. 인스턴스를 공유할 때 가장 주의해야 할 점은 '공유하고 있는 것은 변경하면 여러 장소에 영향을 미친다'는 점이다. 죽, 하나의 인스턴스를 변경하면 그 인스턴스를 사용하고 있는 여러 장소에 동시에 영향이 미친다. 여러 장소에 영향을 미치는 것이 항상 나쁜 것은 아니고, 프로그램이 취급하는 문제에 따라서 좋은 점도 있고, 나쁜 점도 있다. 어쨋든 공유는 '하나를 변경하면 그것을 사용하고 있는 장소 전체에 영향을 미친다'라는 특성을 가지고 있다.

따라서, Flyweight 역할에게 제공하는 정보는 신중히 선택해야 한다. 반드시 여러 장소에 공유시켜야 할 정보만을 Flyweight 역할에게 제공하는 것이 좋다.

### 2) intrinsic와 extrinsic
공유시키는 정보는 intrinsic한 정보라고 한다. intrinsic란 '원래 구비하고 있다', '본질적인'이라는 의미이다. 다시 말하면, 인스턴스를 어디에서 가지고 있더라도 어떠한 상황에서도 변하지 않는 정보, 상태에 의존하지 않는 정보이다. BigChar의 필드 데이터는 BigString의 어디에 등장해도 변하지 않는다. 따라서 BigChar의 폰트 데이터는 intrinsic한 정보가 된다.

한편, 공유시키지 않는 정보는 extrinsic한 정보라고 한다. extrinsic이란 '외부에서 온', '비본질적인'이란 의미이다. 인스턴스를 두는 장소에 따라서 변화하는 정보, 상황에 따라서 변화하는 정보, 상태에 의존하는 정보라는 의미이다. 두는 상소에 따라서 변하는 정보를 공유시킬 수는 없다.

> **intrinsic한 정보:** 장소나 상황에 의존하지 않기 때문에 공유할 수 있다.
>
> **extrinsic한 정보:** 장소나 상황에 의존하기 때문에 공유할 수 없다.

### 3) 인스턴스와 garbage collection
BigCharFactory에서는 java.util.HashMap을 사용해서 생성한 BigChar의 인스턴스를 관리하고 있다. 이와 같이 '인스턴스를 관리'하는 기능을 Java에서 실현할 때에는 반드시 '관리되고 있는 인스턴스는 garbage collection되지 않는다'라는 점을 주의해야 한다.

garbage collection의 기능 덕분에 Java 프로그래머는 일단 new한 인스턴스를 그대로 방치할 수 있다(C++에서는 메모리를 new로 확보한 경우 프로그래머가 명시적으로 delete로 해제할 필요가 있다. 그러나 Java에서는 delete를 할 필요가 없으며, Java에는 원래 delete가 없다).

여기에서 핵심은 'garbage collection의 사용되지 않는 인스턴스를 해제한다'는 부분이다. garbage collection을 실행할 때에 각 인스턴스에 대해서 garbage인지 아닌지의 판정이 이루어지지만, 외부에서 참조되고 있는 인스턴스는 '사용되고 있다'고 간주되어 garbage로 판정되지 않는다.

인스턴스를 명시적으로 삭제할 수는 없지만, 인스턴스에 대한 참조를 없앨 수는 있다. 관리되고 있는 인스턴스를 garbage로 하기 위해서는, 명시적으로 참조를 없애서 관리되지 않도록 할 필요가 있다. 예를 들어, HashMap으로부터 그 인스턴스를 포함하는 엔트리를 삭제하면 인스턴스에 대한 참조를 없앨 수 있다.

### 4) 메모리 이외의 리소스
인스턴스를 공유하면 메모리 사용량을 줄일 수 있다고 하였다. 보다 일반적으로 말하면 인스턴스를 공유하면 인스턴스를 생성하기 위한 리소스의 양을 줄일 수가 있다. 리소스란 컴퓨터 상의 자원이고, 메모리는 리소스의 일종이다.

시간도 리소스의 일종이다. 인스턴스를 new할 때에 일정 시간이 걸린다고 하면, Flyweight 패턴을 사용해서 인스턴스를 공유하면 인스턴스를 new하는 수를 줄일 수 있다. 따라서, 프로그램의 속도를 올릴 수 있다.

파일 핸들(파일 디스크립터)이나 윈도우 패널 등도 리소스의 일종이다. OS에 따라서는 동시에 사용할 수 있는 파일 핸들이나 윈도우 패널의 수에 제한을 두기도 한다. 이때, 인스턴스를 공유해 놓지 않으면 그 제한 때문에 프로그램이 동작하지 않을 위험성이 있다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://dzone.com/articles/design-patterns-flyweight>
