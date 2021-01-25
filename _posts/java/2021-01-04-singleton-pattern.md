---
title: Singleton Pattern
author: Hyosik
date: 2021-01-04 09:30:00 +0900
categories: [Language, DesignPattern]
tags: [singleton-pattern, singleton, design-pattern, 싱글턴-패턴, 디자인-패턴]
---

## 1. Singleton 패턴이란
프로그램을 실행할 때 보통은 많은 인스턴스가 생성된다. 예를 들어 문자열을 표시하는 java.lang.String 클래스의 인스턴스는 문자열 1개에 대해서 1개가 생성되기 때문에 문자열이 1000개 등장하는 프로그램이라면 1000개의 인스턴스가 만들어진다.

그러나 '클래스의 인스턴스가 단 하나만 필요'한 경우도 있다. 그것은 시스템 안에서 1개밖에 존재하지 않는 것을 프로그램으로 표현하고 싶을 때이다. 예를 들어 컴퓨터 자체를 표현한 클래스, 현재의 시스템 설정을 표현한 클래스, 윈도우 시스템을 표현한 클래스 등이다.

* 지정한 클래스의 인스턴스가 '절대로' 1개밖에 존재하지 않는 것을 '보증'하고 싶을 때

* 인스턴스가 1개밖에 존재하지 않는 것을 프로그램 상에서 표현하고 싶을 때

인스턴스가 한 개밖에 존재하지 않는 것을 보증하는 패턴을 _**Singleton 패턴**_ 이라고 한다. singleton이란 '요소를 1개밖에 가지고 있지 않은 집합'을 의미한다.

## 2. 객체

* Singleton 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-04-singleton-pattern/img001.png)

### 1) Singleton의 역할
Singleton 패턴에는 Singleton의 역할만이 존재한다. Singleton 역할은 유일한 인스턴스를 얻기 위한 static 메소드를 가지고 있다. 이 메소드는 언제나 동일한 인스턴스를 반환한다.

## 3. 예제

### 1) Singleton 클래스
인스턴스를 1개 밖에 만들 수 없으며, singleton은 static 필드(클래스 변수)로서 Singleton 클래스의 인스턴스에서 초기화된다. 이 초기화는 Singleton 클래스를 로드할 때 1회만 실행된다.

Singleton 클래스의 생성자는 private로 되어 있다. 이것은 Singleton 클래스 외부에서 생성자의 호출을 금지하기 위해서이다. Singleton 패턴은 프로그래머가 실수를 해도 인스턴스가 1개만 생성되도록 보증하는 패턴이다. 이것을 보증하기 위해서 생성자를 private로 해둘 필요가 있다.

```java
public class Singleton {
    private static Singleton singleton = new Singleton();
    private Singleton() {                                 
        System.out.println("인스턴스를 생성했습니다.");
    }
    public static Singleton getInstance() {
        return singleton;
    }
}
```

### 2) Main 클래스

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Start.");
        Singleton obj1 = Singleton.getInstance();
        Singleton obj2 = Singleton.getInstance();
        if (obj1 == obj2) {
            System.out.println("obj1과 obj2는 같은 인스턴스입니다.");
        } else {
            System.out.println("obj1과 obj2는 같은 인스턴스가 아닙니다.");
        }
        System.out.println("End.");
    }
}
```

## 4. 개념 확장

### 1) 제한할 필요가 있는 이유
Singleton 패턴에서는 인스턴스의 수를 제안하고 있다. 제한을 한다는 것은 전제가 되는 조건을 늘린다는 의미이다.

복수의 인스턴스가 존재하면 인스턴스들이 서로 영향을 미치고, 뜻하지 않은 버그가 발생할 가능성이 있다. 그러나 인스턴스가 1개밖에 없다라는 보증이 있으면 그 전제조건 아래에서 프로그래밍할 수 있다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://commons.wikimedia.org/wiki/File:Singleton_UML_class_diagram.svg>
