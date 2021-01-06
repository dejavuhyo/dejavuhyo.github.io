--- 
title: Builder Pattern
author: Hyosik
date: 2021-01-06 11:00:00 +0900
categories: [Language, Java]
tags: [builder-pattern, builder, design-pattern, 빌더-패턴, 디자인-패턴]
---

## 1. Builder 패턴이란
구조를 가지고 있는 커다란 것을 건축하거나 구축하는 것을 build라고 한다. 빌딩을 세울 때 우선 지반을 다지고, 골격을 세우고, 아래에서 위로 조금씩 만들어 간다. 일반적으로 복잡한 구조물을 세울 때 한번에 완성시키기는 어렵다. 우선 전체를 구성하고 있는 각 부분을 만들고 단계를 밟아 만들어 간다. 구조를 가진 인스턴스를 쌓아 올리는 것을 _**Builder 패턴**_ 이라고 한다.

## 2. 객체

* Prototype 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-06-builder-pattern/img001.png)

### 1) Builder(건축자)의 역할
인스턴스를 생성하기 위한 인터페이스(API)를 결정한다. Builder 역할에는 인스턴스의 각 부분을 만들기 위한 메소드가 준비되어 있다.

### 2) ConcreteBuilder(구체적인 건축자)의 역할
Builder 역할의 인터페이스(API)를 구현하고 있는 클래스이다. 실제의 인스턴스 작성으로 호출되는 메소드가 여기에 정의된다. 또한 최종적인 결과를 얻기 위한 메소드가 준비되어 있다.

### 3) Director(감독자)의 역할
Builder 역할의 인터페이스(API)를 사용해서 인스턴스를 생성한다. ConcreteBuilder 역할에 의존한 프로그래밍은 수행하지 않는다. ConcreteBuilder 역할과 관계없이 제대로 가능하도록 Builder 역할의 메소드만을 사용한다.

### 4) Client(의뢰인)의 역할
Builder 패턴을 이용하는 역할이다

## 3. 예제

* Builder 클래스
  - '문서'를 만들 메소드들을 선언하고 있는 추상 클래스이다. makrTitle, makeString, makeItems는 각각 타이틀, 문자열, 개별 항목을 문서 안에 구축하는 메소드이다. close 메소드는 문서를 완성시키는 메소드이다.

```java
public abstract class Builder {
    public abstract void makeTitle(String title);
    public abstract void makeString(String str);
    public abstract void makeItems(String[] items);
    public abstract void close();
}
```

* Director 클래스
  - Builder 클래스로 선언되어 있는 메소드를 사용해서 문서를 만든다. Director 클래스의 생성자의 인수는 Builder 형이다. 그러나 실세로는 Builder 클래스의 인스턴스가 인수로 주어지는 경우는 없다. 이유는 Builder 클래스는 추상 클래스이므로 인스턴스를 만들 수 없기 때문이다.
  - Director의 생성자에게 실제로 전달되는 것은 Builder 클래스의 하위 클래스(TextBuilder 클래스나 HTMLBuilder 클래스 등)의 인스턴스이다. 주어진 Builder 클래스의 하위 클래스 종류에 따라 Director 클래스가 만들 구체적인 문서의 형식이 정해진다.
  - construct 메소드는 문서를 만드는 메소드이며, Builder에서 선언되어 있는 메소드만을 사용한다. 이 메소드를 호출하면 문서가 만들어진다.

```java
public class Director {
    private Builder builder;
    public Director(Builder builder) { // Builder의 Subclass의 인스턴스가 주어지므로
        this.builder = builder; // builder 필드에 저장해 둔다.
    }
    public void construct() { // 문서구축
        builder.makeTitle("Greeting"); // 타이틀
        builder.makeString("아침과 낮에"); // 문자열
        builder.makeItems(new String[]{ // 개별항목
            "좋은 아침입니다",
            "안녕하세요",
        });
        builder.makeString("밤에"); // 별도의 문자열
        builder.makeItems(new String[]{ // 별도의 개별항목
            "안녕하세요",
            "안녕히 주무세요",
            "안녕히 계세요",
        });
        builder.close(); // 문서를 완성시킨다
    }
}
```

* TexBuilder 클래스
  - Builder 클래스의 하위 클래스이다. 일반 텍스트를 사용해서 문서를 구축하고 결과는 String으로 반환한다.

```java
public class TextBuilder extends Builder {
    private StringBuffer buffer = new StringBuffer(); // 필드의 문서를 구축한다.
    public void makeTitle(String title) { // 일반 텍스트의 제목
        buffer.append("==============================\n"); // 장식선
        buffer.append("『" + title + "』\n"); // 『』 사용한 타이틀
        buffer.append("\n"); // 빈행
    }
    public void makeString(String str) { // 일반 텍스트에서의 문자열
        buffer.append('■' + str + "\n"); // ■ 글머리 기호 붙은 문자열
        buffer.append("\n"); // 빈 행
    }
    public void makeItems(String[] items) { // 일반 텍스트에서의 개별항목
        for (int i = 0; i < items.length; i++) {
            buffer.append(" ㆍ" + items[i] + "\n"); // ㆍ 글머리 기호 붙은 항목
        }
        buffer.append("\n"); // 빈 행
    }
    public void close() { // 문서의 완성
        buffer.append("==============================\n"); // 장식선
    }
    public String getResult() { // 완성한 문서
        return buffer.toString(); // StringBuffer을 String으로 변환
    }
}
```

* HTMLBuilder 클래스
  - Builder 클래스의 하위 클래스이다. HTMLBuilder 클래스는 HTML 파일로 문서를 구축한다. 구축한 결과는 HTML 파일의 파일명으로 반환한다.

```java
import java.io.*;

public class HTMLBuilder extends Builder {
    private String filename; // 작성할 파일명
    private PrintWriter writer; // 파일에 쓸 PrintWriter
    public void buildTitle(String title) { // HTML 파일의 타이틀
        filename = title + ".html"; // 타이틀을 기초로 파일명을 결정
        try {
            writer = new PrintWriter(new FileWriter(filename)); // PrintWriter을 만든다
        } catch (IOException e) {
            e.printStackTrace();
        }
        writer.println("<html><head><title>" + title + "</title></head><body>"); // 타이틀 출력
        writer.println("<h1>" + title + "</h1>");
    }
    public void buildString(String str) { // HTML 파일에서의 문자열
        writer.println("<p>" + str + "</p>"); // <p> 태그로 출력
    }
    public void buildItems(String[] items) { // HTML 파일에서의 개별항목
        writer.println("<ul>"); // <ul>과 <li>로 출력
        for (int i = 0; i < items.length; i++) {
            writer.println("<li>" + items[i] + "</li>");
        }
        writer.println("</ul>");
    }
    public void buildDone() { // 문서의 완성
        writer.println("</body></html>"); // 태그를 닫는다
        writer.close(); // 파일을 닫는다
    }
    public String getResult() {
        return filename; // 파일명을 반환한다
    }
}
```

* Main 클래스
  - Builder 패턴의 테스트 프로그램이다. 커맨드 라인에서 plain을 지정한 경우에는 TextBuilder 클래스의 인스턴스를 Director 클래스의 생성자에게 전달한다. 또한 커맨드 라인에서 html을 지정한 경우에는 HTMLBuilder 클래스의 인스턴스를 Director 클래스의 생성자에게 전달한다.
  - TextBuilder와 HTMLBuilder는 Builder의 하위 클래스이고 Director는 Builder의 메소드만을 사용해서 문서를 작성한다. Builder의 메소드만을 사용한다는 것은 Director는 실제로 동작하는 것이 TextBuilder인지, HTMLBuilder인지 모른다는 의미이다.

따라서 Builder는 문서를 구축하려는 목적을 달성하기 위해서 필요 충분한 메소드군을 선언할 필요가 있다. 단, 일반 텍스트나 HTML 파일에 고유의 메소드까지 Builder가 제공해서는 안된다.

```java
public class Main {
    public static void main(String[] args) {
        if (args.length != 1) {
            usage();
            System.exit(0);
        }
        if (args[0].equals("plain")) {
            TextBuilder textbuilder = new TextBuilder();
            Director director = new Director(textbuilder);
            director.construct();
            String result = textbuilder.getResult();
            System.out.println(result);
        } else if (args[0].equals("html")) {
            HTMLBuilder htmlbuilder = new HTMLBuilder();
            Director director = new Director(htmlbuilder);
            director.construct();
            String filename = htmlbuilder.getResult();
            System.out.println(filename + "가 작성되었습니다.");
        } else {
            usage();
            System.exit(0);
        }
    }
    public static void usage() {
        System.out.println("Usage: java Main plain 일반 텍스트로 문서작성");
        System.out.println("Usage: java Main html  HTML 파일로 문서작성");
    }
}
```

## 4. 설계 시에 결정할 수 있는 것과 결정할 수 없는 것
Builder 클래스는 문서를 구축할 때(목적을 달성하기 위해) 필요 충분한 메소드 군을 선언해야 한다. Director 클래스에 주어진 도구는 Builder 클래스가 제공하는 도구이기 때문에 Builder 클래스의 메소드로서 무엇을 준비해야 할지는 중요하다. 더구나 Builder 클래스는 앞으로 늘어날지도 모르는 Builder 클래스의 하위 클래스의 요구에도 대응할 필요가 있다.

클래스 설계자는 미래에 일어날 모든 것을 예상할 수 없다. 단지, 미래에 발생할 것으로 예상되는 변화에 적응할 수 있도록 설계해야 한다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://commons.wikimedia.org/wiki/Prototype_(design_pattern)>
