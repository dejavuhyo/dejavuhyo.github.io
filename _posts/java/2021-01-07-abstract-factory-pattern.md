--- 
title: Abstract Factory Pattern
author: Hyosik
date: 2021-01-07 10:50:00 +0900
categories: [Language, Java]
tags: [abstract-factory-pattern, abstract-factory, design-pattern, 추상-팩토리-패턴, 디자인-패턴]
---

## 1. Abstract Factory 패턴이란
Abstract는 '추상적인'이라는 의미이고, Factory는 '공장'이라는 의미이다. 즉, abstract factory는 '추상적인 공장'이라는 의미이다.

Abstract Factory 패턴에서는 추상적인 공장이 등장하고, 추상적인 부품을 조합해서 추상적인 제품을 만든다. 즉, 부품의 구체적인 구현에는 주목하지 않고 인터페이스(API)에 주목한다. 그리고 인터페이스(API)만을 사용해서 부품을 조립하고 제품으로 완성한다. 이러한 패턴을 _**Abstract Factory 패턴**_ 이라고 한다.

## 2. 객체

* Abstract 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-07-abstract-factory-pattern/img001.png)

### 1) AbstractProduct(추상적인 제품)의 역할
AbstractFactory 역할에 의해 만들어지는 추상적인 부품이나 제품의 인터페이스(API)를 결정한다.

### 2) AbstractFactory(추상적인 공장)의 역할
AbstractProduct 역할의 인스턴스를 만들어 내기 위한 인터페이스(API)를 결정한다.

### 3) Client(의뢰자)의 역할
AbstractFactory 역할과 AbstractProduct 역할의 인터페이스(API)만을 사용해서 주어진 역할을 실행한다. Client는 구체적인 부품이나 제품, 공장에 대해서는 모른다.

### 4) ConcreteProduct(구체적인 제품)의 역할
AbstractProduct 역할의 인터페이스(API)를 구현한다.

### 5) ConcreteFactory(구체적인 공장)의 역할
ConcreteFactory 역할의 인터페이스(API)를 구현한다.

## 3. 예제

* Item 클래스
  - Link와 Tray의 상위 클래스이다. 이것은 Link와 Tray를 동일시하기 위한 클래스이다. caption 필드는 이 항목의 '목차'를 표시한다. makeHTML 메소드는 추상 메소드로 하위 클래스에서 구현되도록 한다. 이 메소드를 호출하면 HTML의 문자열이 반환값이 된다.

```java
package factory;

public abstract class Item {
    protected String caption;
    public Item(String caption) {
        this.caption = caption;
    }
    public abstract String makeHTML();
}
```

* Link 클래스
  - HTML의 하이퍼링크를 추상적으로 표현한 클래스이다. url 필드는 링크되는 곳의 URL을 저장하기 위한 것이다. Link 클래스에서는 추상 메소드가 전혀 등장하지 않는 것처럼 보이지만, 상위 클래스(Item)의 추상 메소드(makeHTML)가 구현되어 있지 않으므로 Link 클래스도 추상 클래스이다.

```java
package factory;

public abstract class Link extends Item {
    protected String url;
    public Link(String caption, String url) {
        super(caption);
        this.url = url;
    }
}
```

* Tray 클래스
  - 복수의 Link나 Tray를 모아서 합친 것을 표시한 클래스이다. Link와 Tray는 add 메소드를 사용해서 모은다. 'Link나 Tray'라는 부분을 표현하기 위해 add 메소드에서는 Link와 Tray의 상위 클래스인 Item을 인수로 갖는다. Tray 클래스도 Item 클래스의 추상 메소드 makeHTML을 상속하고 있지만 구현은 하지 않는다. 그러므로 Tray 클래스는 추상 클래스가 된다.

```java
package factory;
import java.util.ArrayList;

public abstract class Tray extends Item {
    protected ArrayList tray = new ArrayList();
    public Tray(String caption) {
        super(caption);
    }
    public void add(Item item) {
        tray.add(item);
    }
}
```

* Page 클래스
  - HTML 페이지 전체를 추상적으로 표현한 클래스이다. Link나 Tray가 추상적인 '부품'이라면 Page 클래스는 추상적인 '제품'에 해당한다. title은 페이지의 제목, author는 페이지의 저자를 표현하기 위한 필드이다.
  - 저자 이름은 생성자에서 인수로 지정한다. 페이지에는 add 메소드를 사용해서 Item(즉, Link 혹은 Tray)을 추가한다. 추가한 것이 이 페이지에서 표시된다.
  - output 메소드 안에서는 제목을 기초로 파일명을 결정하고 makeHTML의 메소드를 사용해서 자신의 HTML의 내용을 파일에 기술하고 있다.

```java
package factory;
import java.io.*;
import java.util.ArrayList;

public abstract class Page {
    protected String title;
    protected String author;
    protected ArrayList content = new ArrayList();
    public Page(String title, String author) {
        this.title = title;
        this.author = author;
    }
    public void add(Item item) {
        content.add(item);
    }
    public void output() {
        try {
            String filename = title + ".html";
            Writer writer = new FileWriter(filename);
            writer.write(this.makeHTML());
            writer.close();
            System.out.println(filename + " 을 작성했습니다.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public abstract String makeHTML();
}
```

* Factory 클래스
  - getFactory 메소드는 클래스의 이름을 지정해서 구체적인 공장의 인스턴스를 작성한다. getFactory 안에서는 Class 클래스의 forName 메소드를 사용해서, 그 클래스를 동적으로 읽는다. 그리고 newInstance 메소드를 이용해서, 그 클래스의 인스턴스를 한 개 작성한다. 이것이 getFactory의 반환값이 된다.
  - Class 클래스는 java.lang 패키지에 속하는 클래스로서 '클래스를 표현하는 클래스'이다. Class 클래스는 Java의 표준 라이브러리에 포함되어 있다. forName은 java.lang.Class의 클래스 메소드(static 메소드)이고, newInstance는 java.lang.Class의 인스턴스 메소드이다.
  - getFactory 메소드의 안에는 구체적인 공장의 인스턴스를 만들지만, 반환값은 추상적인 공자이라는 점에 주의해야한다.
  - createLink, createTray, createPage의 각 메소드는 그 추상적인 공장에서 부품이나 제품을 작성할 때 이용하는 메소드이다. 이것은 모두 추상 메소드로 되어 있고, 실제의 구체적인 부품이나 제품의 작성은 Factory의 하위 클래스에게 맡기고 있다. 단, 메소드의 이름과 시그니처만은 확실히 정해져 있다.

```java
package factory;

public abstract class Factory {
    public static Factory getFactory(String classname) {
        Factory factory = null;
        try {
            factory = (Factory)Class.forName(classname).newInstance();
        } catch (ClassNotFoundException e) {
            System.err.println("클래스 " + classname + " 이 발견되지 않습니다.");
        } catch (Exception e) {
            e.printStackTrace();
        }
        return factory;
    }
    public abstract Link createLink(String caption, String url);
    public abstract Tray createTray(String caption);
    public abstract Page createPage(String title, String author);
}
```

* Main 클래스
  - 추상적인 공장을 사용해서 추상적인 제품을 제조하고, 추상적인 제품을 조립하고 있다. import 되어 있는 것이 factory 패키지뿐인 점에서 알 수 있듯이 이 클래스에서는 구체적인 부품, 제품, 공장을 전혀 이용하지 않는다. 구체적인 공장의 클래스 이름은 커맨드 라인에서 지정한다.

```java
import factory.*;

public class Main {
    public static void main(String[] args) {
        if (args.length != 1) {
            System.out.println("Usage: java Main class.name.of.ConcreteFactory");
            System.out.println("Example 1: java Main listfactory.ListFactory");
            System.out.println("Example 2: java Main tablefactory.TableFactory");
            System.exit(0);
        }
        Factory factory = Factory.getFactory(args[0]);

        Link joins = factory.createLink("중앙일보", "http://www.joins.com/");
        Link chosun = factory.createLink("조선일보", "http://www.chosun.com/");

        Link us_yahoo = factory.createLink("Yahoo!", "http://www.yahoo.com/");
        Link kr_yahoo = factory.createLink("Yahoo!Korea", "http://www.yahoo.co.kr/");
        Link excite = factory.createLink("Excite", "http://www.excite.com/");
        Link google = factory.createLink("Google", "http://www.google.com/");

        Tray traynews = factory.createTray("신문");
        traynews.add(joins);
        traynews.add(chosun);

        Tray trayyahoo = factory.createTray("Yahoo!");
        trayyahoo.add(us_yahoo);
        trayyahoo.add(kr_yahoo);

        Tray traysearch = factory.createTray("검색엔진");
        traysearch.add(trayyahoo);
        traysearch.add(excite);
        traysearch.add(google);

        Page page = factory.createPage("LinkPage", "영진닷컴");
        page.add(traynews);
        page.add(traysearch);
        page.output();
    }
}
```

* ListFactory 클래스
  - Factory 클래스의 추상 메소드 createLink, createTray, createPage를 구현하고 있다. 여기에서는 단순하게 ListLink, ListTray, ListPage를 new하고 있다.

```java
package listfactory;
import factory.*;

public class ListFactory extends Factory {
    public Link createLink(String caption, String url) {
        return new ListLink(caption, url);
    }
    public Tray createTray(String caption) {
        return new ListTray(caption);
    }
    public Page createPage(String title, String author) {
        return new ListPage(title, author);
    }
}
```

* ListLink 클래스
  - Link 클래스의 하위 클래스이다. 구현해야 할 메소드는 상위 클래스에서 추상 메소드였던 makeHTML이다. ListLink에서는 <li> 태그와 <a> 태그를 사용해서 HTML의 일부분을 작성하고 있다.

```java
package listfactory;
import factory.*;

public class ListLink extends Link {
    public ListLink(String caption, String url) {
        super(caption, url);
    }
    public String makeHTML() {
        return "  <li><a href=\"" + url + "\">" + caption + "</a></li>\n";
    }
}
```

* ListTray 클래스
  - Tray 클래스의 하위 클래스이다. tray 필드 안에는 HTML로 출력해야 할 Item 들이 모여있다. 그것들을 HTML의 태그로 표현하는 것이 이 makeHTML 메소드의 사명이다.
  - 변수 item의 내용이 실제로 무엇인지를 조사해서 switch문이나 if문을 사용하는 프로그램을 작성해서는 안된다. 그것은 매우 비오브젝트(비객체) 지향적인 프로그램이 되고만다. 변수 item은 Item형이고 Item 클래스에서는 makeHTML이라는 메소드가 선언되고 있다. 그리고 ListLink나 ListTray는 모두 Item 클래스의 하위 클래스이다. 그러므로 안심하고 makeHTML 메소드를 호출하면 된다.
  - 여기에서 사용되고 있는 java.util.Iterator라는 클래스는 Iterator 패턴에서 배운 것과 기능적으로는 같지만, 여기에서는 Java의 클래스 라이브러리로 제공되고 있는 것이다. java.util.ArrayList 클래스에서 java.util.Iterator 클래스를 만들기 위해서는 iterator라는 메소드가 사용된다.

```java
package listfactory;
import factory.*;
import java.util.Iterator;

public class ListTray extends Tray {
    public ListTray(String caption) {
        super(caption);
    }
    public String makeHTML() {
        StringBuffer buffer = new StringBuffer();
        buffer.append("<li>\n");
        buffer.append(caption + "\n");
        buffer.append("<ul>\n");
        Iterator it = tray.iterator();
        while (it.hasNext()) {
            Item item = (Item)it.next();
            buffer.append(item.makeHTML());
        }
        buffer.append("</ul>\n");
        buffer.append("</li>\n");
        return buffer.toString();
    }
}
```

* ListPage 클래스
  - Page 클래스의 하위 클래스이다. ListPage는 필드의 내용을 사용해 페이지를 구성하고 있다. 저자 이름(author)은 <address> 태그를 사용해 표현하고 있다.

```java
package listfactory;
import factory.*;
import java.util.Iterator;

public class ListPage extends Page {
    public ListPage(String title, String author) {
        super(title, author);
    }
    public String makeHTML() {
        StringBuffer buffer = new StringBuffer();
        buffer.append("<html><head><title>" + title + "</title></head>\n");
        buffer.append("<body>\n");
        buffer.append("<h1>" + title + "</h1>\n");
        buffer.append("<ul>\n");
        Iterator it = content.iterator();
        while (it.hasNext()) {
            Item item = (Item)it.next();
            buffer.append(item.makeHTML());
        }
        buffer.append("</ul>\n");
        buffer.append("<hr><address>" + author + "</address>");
        buffer.append("</body></html>\n");
        return buffer.toString();
    }
}
```

* TableFactory 클래스
  - Factory 클래스의 하위 클래스이다. createLink, createTray, createPage에서는 각각 TableLink, TableTray, TablePage라는 클래스의 인스턴스를 만들고 있다.

```java
package tablefactory;
import factory.*;

public class TableFactory extends Factory {
    public Link createLink(String caption, String url) {
        return new TableLink(caption, url);
    }
    public Tray createTray(String caption) {
        return new TableTray(caption);
    }
    public Page createPage(String title, String author) {
        return new TablePage(title, author);
    }
}
```

* TableLink 클래스
  - Link 클래스의 하위 클래스이다. makeHTML에서는 테이블의 1열을 구성하는 <td> 태그를 사용하고 있다.

```java
package tablefactory;
import factory.*;

public class TableLink extends Link {
    public TableLink(String caption, String url) {
        super(caption, url);
    }
    public String makeHTML() {
        return "<td><a href=\"" + url + "\">" + caption + "</a></td>\n";
    }
}
```

* TableTray 클래스
  - Tray 클래스의 하위 클래스이다. makeHTML에서는 <td>와 <table> 태그를 사용해서 Item들을 출력하고 있다.

```java
package tablefactory;
import factory.*;
import java.util.Iterator;

public class TableTray extends Tray {
    public TableTray(String caption) {
        super(caption);
    }
    public String makeHTML() {
        StringBuffer buffer = new StringBuffer();
        buffer.append("<td>");
        buffer.append("<table width=\"100%\" border=\"1\"><tr>");
        buffer.append("<td bgcolor=\"#cccccc\" align=\"center\" colspan=\""+ tray.size() + "\"><b>" + caption + "</b></td>");
        buffer.append("</tr>\n");
        buffer.append("<tr>\n");
        Iterator it = tray.iterator();
        while (it.hasNext()) {
            Item item = (Item)it.next();
            buffer.append(item.makeHTML());
        }
        buffer.append("</tr></table>");
        buffer.append("</td>");
        return buffer.toString();
    }
}
```

* TablePage 클래스
  - Page 클래스의 하위 클래스이다.

```java
package tablefactory;
import factory.*;
import java.util.Iterator;

public class TablePage extends Page {
    public TablePage(String title, String author) {
        super(title, author);
    }
    public String makeHTML() {
        StringBuffer buffer = new StringBuffer();
        buffer.append("<html><head><title>" + title + "</title></head>\n");
        buffer.append("<body>\n");
        buffer.append("<h1>" + title + "</h1>\n");
        buffer.append("<table width=\"80%\" border=\"3\">\n");
        Iterator it = content.iterator();
        while (it.hasNext()) {
            Item item = (Item)it.next();
            buffer.append("<tr>" + item.makeHTML() + "</tr>");
        }
        buffer.append("</table>\n");
        buffer.append("<hr><address>" + author + "</address>");
        buffer.append("</body></html>\n");
        return buffer.toString();
    }
}
```

## 4. 구체적인 공장을 새로 추가하는 것은 간단하다.
Abstract Factory 패턴에 구체적인 공장을 새로 추가하는 것은 간단하다. '간단'하다는 것은 어떤 클래스를 만들고, 어떤 메소드를 구현하면 좋은지가 확실하다는 의미이다. 

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://narbase.com/2020/06/07/design-patterns-abstract-factory-with-kotlin-examples/>
