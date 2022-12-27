---
title: Java super 키워드
author: dejavuhyo
date: 2021-12-10 09:50:00 +0900
categories: [Language, Java]
tags: [java-super, java-super-keyword, super-keyword, super-키워드, 자바-super]
---

## 1. super 키워드
super 키워드를 사용하여 상위 클래스에 액세스 할 수 있다.

## 2. 생성자가 있는 슈퍼 키워드
super()를 사용 하여 부모 기본 생성자를 호출 할 수 있다. 생성자의 첫 번째 명령문이어야 한다.

이 예에서는 String 인수와 함께 super(message)를 사용한다.

```java
public class SuperSub extends SuperBase {

    public SuperSub(String message) {
        super(message);
    }
}
```

자식 클래스 인스턴스를 만들고 뒤에서 무슨 일이 일어나는지 본다.

```java
SuperSub child = new SuperSub("message from the child class");
```

새로운 키워드는 SuperSub의 생성자를 호출하며, 그 자체가 부모 생성자를 먼저 호출하고 문자열 인수를 전달한다.

## 3. 부모 클래스 변수에 접근
메시지 인스턴스 변수를 사용하여 부모 클래스를 생성한다.

```java
public class SuperBase {
    String message = "super class";
}
```

이제 같은 이름의 변수를 사용하여 자식 클래스를 만든다.

```java
public class SuperSub extends SuperBase {

    String message = "child class";

    public void getParentMessage() {
        System.out.println(super.message);
    }
}
```

super 키워드 사용하여 자식 클래스에서 부모 변수에 액세스할 수 있다.

## 4. 메서드 재정의가 있는 슈퍼 키워드
부모 클래스에 인스턴스 메서드를 추가한다.

```java
public class SuperBase {

    String message = "super class";

    public void printMessage() {
        System.out.println(message);
    }
}
```

그리고 자식 클래스에서 printMessage() 메서드를 재정의한다.

```java
public class SuperSub extends SuperBase {

    String message = "child class";

    public SuperSub() {
        super.printMessage();
        printMessage();
    }

    public void printMessage() {
        System.out.println(message);
    }
}
```

슈퍼를 사용 하여 자식 클래스에서 재정의된 메서드에 액세스 할 수 있다. 생성자의 super.printMessage()는 SuperBase에서 부모 메서드를 호출한다.

## [출처 및 참고]
* <https://www.baeldung.com/java-super>
