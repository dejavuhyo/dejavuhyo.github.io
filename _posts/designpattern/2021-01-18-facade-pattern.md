---
title: Facade Pattern
author: dejavuhyo
date: 2021-01-18 11:20:00 +0900
categories: [Language, DesignPattern]
tags: [facade-pattern, facade, design-pattern, 퍼사드-패턴, 디자인-패턴]
---

## 1. Facade 패턴이란
프로그램이라는 것은 점점 커지는 경향이 있다. 많은 크래스가 만들어져 서로 관계를 맺으면서 복잡하게 된다. 클래스를 사용할 경우에는 클래스 간의 관계를 정확히 이해하고 정확한 순서대로 메소드를 호출할 필요가 있다.

커다란 프로그램을 사용해서 처리를 실행하려면 상호 관련된 많은 클래스를 적절하게 제어해야 한다. 그렇다면 그 처리를 실행하기 위한 '창구'를 준비해 두는 것이 좋다. 그렇게 하면 많은 클래스를 개별적으로 제어하지 않아도 그 '창구'에 대해서만 요구하면 역할이 끝나기 때문이다. 이러한 '창구'가 _**Facade 패턴**_ 이다.

Facade 패턴은 복잡하게 얽혀 있는 것을 정리해서 높은 레벨의 인터페이스(API)를 제공한다. Facade 역할은 시스템의 외부에 대해서는 단순한 인터페이스(API)를 보여준다. 또한 시스템 내부에 있는 각 클래스의 역할이나 의존관계를 생각해서 정확한 순서로 클래스를 이용한다.

## 2. 객체

* Facade 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-18-facade-pattern/img001.png)

### 1) Facade(정면)의 역할
Facade 역할은 시스템을 구성하고 있는 그 밖의 많은 역할에 대해 '단순한 창구'가 된다. Facade 역할은 높은 레벨에서 단순한 인터페이스(API)를 시스템 외부에 제공한다.

### 2) 시스템을 구성하고 있는 그 밖의 많은 역할
시스템을 구성하는 다른 많은 역할은 각각의 임무를 실행하지만 Facade 역할에 대해서는 신경쓰지 않는다. Facade 역할에서 호출되는 임무를 실행하지만, 다른 역할이 Facade 역할을 호출하는 일은 없다.

### 3) Client(의로인)의 역할
Facade 패턴을 이용하는 역할이다.

## 3. 예제

### 1) Database 클래스
데이터베이스 이름을 지정하고 그것에 대응하는 Properties를 작성하는 클래스이다. 이 클래스에서는 인스턴스를 만들지 않고 getProperties라는 static 메소드를 매개로 Properties의 인스턴스를 얻는다.

```java
package pagemaker;

import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;

public class Database {
    private Database() {
    }
    public static Properties getProperties(String dbname) { 
        String filename = dbname + ".txt";
        Properties prop = new Properties();
        try {
            prop.load(new FileInputStream(filename));
        } catch (IOException e) {
            System.out.println("Warning: " + filename + " is not found.");
        }
        return prop;
    }
}
```

### 2) HtmlWriter 클래스
간단한 웹 페이지를 작성한다. 인스턴스 작성 시에 Writer를 제공하고 그 Writer에 대해서 HTML을 출력한다.

title 메소드는 타이틀을 출력하고, paragraph 메소드는 단락을 출력하고, link 메소드는 링크를 출력하고, mailto 메소드는 메일 주소의 링크를 출력한다. 그리고 close 메소드는 HTML의 출력을 끝낸다. 이 클래스에는 title 메소드를 제일 처음 호출해야 하는 제약이 숨겨져 있다. 그리고 창구가 되는 PageMaker 클래스는 그 제약을 지키도록 기술되어 있다.

```java
package pagemaker;

import java.io.Writer;
import java.io.IOException;

public class HtmlWriter {
    private Writer writer;
    public HtmlWriter(Writer writer) {
        this.writer = writer;
    }
    public void title(String title) throws IOException {
        writer.write("<html>");
        writer.write("<head>");
        writer.write("<title>" + title + "</title>");
        writer.write("</head>");
        writer.write("<body>\n");
        writer.write("<h1>" + title + "</h1>\n");
    }
    public void paragraph(String msg) throws IOException {
        writer.write("<p>" + msg + "</p>\n");
    }
    public void link(String href, String caption) throws IOException {
        paragraph("<a href=\"" + href + "\">" + caption + "</a>");
    }
    public void mailto(String mailaddr, String username) throws IOException {
        link("mailto:" + mailaddr, username);
    }
    public void close() throws IOException {
        writer.write("</body>");
        writer.write("</html>\n");
        writer.close();
    }
}
```

### 3) PageMaker 클래스
Database 클래스와 HtmlWriter 클래스를 조합해서 지정한 사용자의 웹 페이지를 작성하기 위한 것이다.

이 클래스에서 정의되어 있는 public인 메소드는 makeWelcomePage 뿐이다. 이 메소드에 메일 주소와 풀력 파일 이름을 지정만하면 웹 페이지가 작성된다.

HtmlWriter 클래스의 메소드를 호출하는 곳은 이 PageMaker 클래스가 혼자 인수해서, 외부에 대해서는 단 하나의 makeWelcomePage 메소드만을 보이고 있다. 이것이 단순한 창구이다.

```java
package pagemaker;

import java.io.FileWriter;
import java.io.IOException;
import java.util.Properties;

public class PageMaker {
    private PageMaker() {   
    }
    public static void makeWelcomePage(String mailaddr, String filename) {
        try {
            Properties mailprop = Database.getProperties("maildata");
            String username = mailprop.getProperty(mailaddr);
            HtmlWriter writer = new HtmlWriter(new FileWriter(filename));
            writer.title("Welcome to " + username + "'s page!");
            writer.paragraph(username + "의 페이지에 오신 걸 환영합니다.");
            writer.paragraph("메일을 기다리고 있습니다.");
            writer.mailto(mailaddr, username);
            writer.close();
            System.out.println(filename + " is created for " + mailaddr + " (" + username + ")");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 4) Main 클래스
pagemaker 패키지의 PageMaker 클래스를 이용한다.

```java
import pagemaker.PageMaker;

public class Main {
    public static void main(String[] args) {
        PageMaker.makeWelcomePage("email@email.com", "welcome.html");
    }
}
```

## 4. 개념 확장

### 1) Facade 역할
Facade는 복잡한 것을 단순하게 보여준다. '복잡한 것'이란 내부에서 실행되고 있는 많은 클래스의 관계나 사용법이 '복잡한 것'을 말한다. Facade 역할은 복잡한 것을 의식하지 않도록 해준다.

여기에서의 핵심은 인터페이스(API)를 적게 하는 일이다. 클래스나 메소드가 많이 보이면, 프로그래머는 무엇을 사용하면 좋을지 망설이게 되고, 호출하는 순서에도 주의해야만 한다. 주의해야 한다는 것은 틀리기 쉽다는 것이다. 따라서 인터페이스(API)가 적은 Facade 역할을 생각하는 것이 좋다.

인터페이스(API)의 수가 적다는 것은 외부와의 결합이 소원하다고 표현할 수 있다. 외부와의 소원한 결합, 약한 결합, 유연한 결합은 패키지(클래스의 집합)를 부품으로써 재이용하기 쉽게 해준다.

클래스를 설계할 때에는 어떤 메소드를 public으로 할지 생각한다. 너무 많은 메소드를 public으로 하면 클래스 내부를 수정하기 힘들게 된다. 필드에 대해서도 마찬가지이다. 뜻하지 않게 필드를 public으로 하면 다른 클래스가 그 필드의 내용을 멋대로 참조하거나 변경하기 때문에 클래스의 수정이 힘들게 된다.

클래스의 설계와 마찬가지로 패키지를 설계할 때에는 어떤 클래스를 public으로 할지 생각할 필요가 있다. 클래스를 지나치게 외부(패키지의 외부)에 노출시키면 패키지 내부를 수정하기 힘들게 된다.

### 2) 재귀적인 Facade 패턴의 적용
클래스에서 보다 큰 단위인 패키지로 눈을 돌려 발상의 전환을 해보자. Facade 역할을 가진 클래스의 집합이 여러개 있다고 가정한다. 이때 클래스의 집합을 정리해서 새로운 Facade 역할을 도입할 수도 있다. 즉, Facade 패컨을 재귀적으로 적용하는 것이다. 상당히 큰 시스템이 다수의 클래스, 다수의 패키지를 포함하고 있을 때 요소요소에 Facade 패턴을 적용하면 시스템은 보다 편리하게 된다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://en.wikipedia.org/wiki/Facade_pattern>
