---
title: Visitor Pattern
author: dejavuhyo
date: 2021-01-14 10:40:00 +0900
categories: [Language, DesignPattern]
tags: [visitor-pattern, visitor, design-pattern, 비지터-패턴, 디자인-패턴]
---

## 1. Visitor 패턴이란
데이터 구조 안에 많은 요소가 저장되어 있고, 그 각 요소에 대해서 무엇인가 '처리'해 간다고 가정한다. 이때 그 '처리'의 코드는 일반적으로 생가하면 데이터 구조를 표시하고 있는 클래스 안에 기술할 것이다. 그러나 만약, 그 '처리'가 여러 종류라면 새로운 처리가 필요할 때마다 데이터 구조의 클래스를 수정해야 한다.

Visitor 패턴에서는 데이터 구조와 처리를 분리한다. 그리고 데이터 구조 안을 돌아다니는 주체인 '방문자'를 나타내는 클래스를 준비해서 그 클래스에게 처리를 위임한다. 그렇다면 새로운 처리를 추가하고 싶을 때에는 새로운 '방문자'를 만들면 된다. 그리고 데이터 구조는 문을 두드리고 있는 '방문자'를 받아들이면 된다. 이러한 디자인 패턴을 _**Visitor 패턴**_ 이라고 한다.

## 2. 객체

* Visitor 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-14-visitor-pattern/img001.png)

### 1) Visitor(방문자)의 역할
데이터 구조의 구체적인 요소(ConcreteElement 역할)마다 'xxxx을 방문했다'라는 visit(xxxx) 메소드를 선언한다. visit(xxxx)는 xxxx을 처리하기 위한 메소드이고, 실제의 코드는 ConcreteVisitor 역할에 기술되어 있다.

### 2) ConcreteVisitor(구체적인 방문자)의 역할
Visitor 역할의 인터페이스(API)를 구현한다. visit(xxxx)라는 형태의 메소드를 구현하고, 각각의 ConcreteElement 역할의 처리를 기술한다.

### 3) Element(요소)의 역할
Visitor 역할의 방문할 곳을 나타내는 역할로, 방문자를 받아들이는 accept 메소드를 선언한다. accept 메소드의 인수에는 Visitor 역할이 전달된다.

### 4) ConcreteElement(구체적 요소)의 역할
Element 역할의 인터페이스(API)를 구현하는 역할이다.

### 5) ObjectStructure(오브젝트의 구조)의 역할
Element 역할의 집합을 취급하는 역할이다. ConcreteVisitor 역할이 각각의 Element 역할을 취급할 수 있는 메소드를 구비하고 있다.

## 3. 예제

### 1) Visitor 클래스
'방문자'를 나타내는 추상 클래스이다. 이 방문자는 방문할 곳의 데이터 구조(즉, File과 Directory)에 의존한다.

Visitor 클래스에서는 visit라는 동일한 이름의 메소드를 두 개 선언한다. 이름은 같지만 인수가 다르며, 하나는 File을 다른 하나는 Directory를 인수로 가진다. 이름이 같아도 메소드 호출을 실행할 때 인수의 형에 따라 자동적으로 메소드의 식별이 이루어진다. 일반적으로 이것을 메소드의 오버로드(overload)라고 한다.

```java
public abstract class Visitor {
    public abstract void visit(File file);
    public abstract void visit(Directory directory);
}
```

### 2) Element 인터페이스
방문자를 받아들이는 인터페이스이다. Element 인터페이스에서 선언하고 있는 메소드는 accept이다. 인수는 방문자인 Visitor 클래스가 된다.

```java
public interface Element {
    public abstract void accept(Visitor v);
}
```

### 3) Entry 클래스
Composite 패턴의 예제 프로그램에서 등장한 것과 본질적으로 같지만 여기에서는 Element 인터페이스를 구현(implements)하고 있다. 이것은 Entry 클래스를 Visitor 패턴에 적용시키기 위해서 이다. Element 인터페이스에서 선언되고 있는 추상 메소드 accept를 실제로 구현하는 것은 Entry의 하위 클래스인 File 클래스와 Directory 클래스이다.

add 메소드는 Directory 클래스에서만 유효하므로 Entry 클래스에서는 에러로 처리한다. 요소를 취득하기 위한 Iterator를 얻을 수 있는 iterator 메소드도 Directory 클래스에서만 유효하기 때문에 add 메소드와 마찬가지로 Entry 클래스에서 에러로 처리한다.

```java
import java.util.Iterator;

public abstract class Entry implements Element {
    public abstract String getName();
    public abstract int getSize();
    public Entry add(Entry entry) throws FileTreatmentException {
        throw new FileTreatmentException();
    }
    public Iterator iterator() throws FileTreatmentException {
        throw new FileTreatmentException();
    }
    public String toString() {
        return getName() + " (" + getSize() + ")";
    }
}
```

### 4) File 클래스
Composite 패턴의 예제 프로그램에서 등장한 클래스와 거의 유사하다. accept 메소드의 Visitor 클래스이고, accept 메소드 안에서 v.visit(this);라는 문으로 Visitor의 visit 메소드를 호출한다. visit 메소드는 오버로드되어 있지만, 여기에서 호출되는 것은 visit(File) 쪽이다. 왜냐면 이곳의 this는 File 클래스의 생성자이기 때문이다.

```java
public class File extends Entry {
    private String name;
    private int size;
    public File(String name, int size) {
        this.name = name;
        this.size = size;
    }
    public String getName() {
        return name;
    }
    public int getSize() {
        return size;
    }
    public void accept(Visitor v) {
        v.visit(this);
    }
}
```

### 5) Directory 클래스
디렉터리를 나타내는 클래스이다. Composite 패턴과 비교해서 두 개의 메소드가 증가하고 있다.

하나는 iterator 메소드이다. iterator 메소드는 디렉터리에 포함되어 있는 디렉터리 엔트리(파일이나 디렉터리)의 종류를 얻기 위한 Iterator를 반환한다.

다른 하나는 accetp 메소드이다. File 클래스의 accept가 visit(File) 메소드를 호출하는 것처럼 Directory 클래스의 accept는 visit(Directory) 메소드를 호출한다. 이렇게 해서 방문자에게 '당신이 방문한 것은 이 Directory의 인스턴스입니다'라고 전하게 된다.

```java
import java.util.Iterator;
import java.util.ArrayList;

public class Directory extends Entry {
    private String name;
    private ArrayList dir = new ArrayList();
    public Directory(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public int getSize() {
        int size = 0;
        Iterator it = dir.iterator();
        while (it.hasNext()) {
            Entry entry = (Entry)it.next();
            size += entry.getSize();
        }
        return size;
    }
    public Entry add(Entry entry) {
        dir.add(entry);
        return this;
    }
    public Iterator iterator() {
        return dir.iterator();
    }
    public void accept(Visitor v) { // 방문자 승낙
        v.visit(this);
    }
}
```

### 6) ListVisitor 클래스
Visitor 클래스의 하위 클래스이고, 데이터 구조를 돌아다니면서 종류를 표시하기 위한 클래스이다. Visitor 클래스의 하위 클래스이기 때문에, visit(File) 메소드와 visit(Directory) 메소드를 구현한다.

```java
import java.util.Iterator;

public class ListVisitor extends Visitor {
    private String currentdir = "";
    public void visit(File file) {
        System.out.println(currentdir + "/" + file);
    }
    public void visit(Directory directory) {
        System.out.println(currentdir + "/" + directory);
        String savedir = currentdir;
        currentdir = currentdir + "/" + directory.getName();
        Iterator it = directory.iterator();
        while (it.hasNext()) {
            Entry entry = (Entry)it.next();
            entry.accept(this);
        }
        currentdir = savedir;
    }
}
```

### 7) FileTreatmentException 클래스
Composite 패턴과 동일하다.

```java
public class FileTreatmentException extends RuntimeException {
    public FileTreatmentException() {
    }
    public FileTreatmentException(String msg) {
        super(msg);
    }
}
```

### 8) Main 클래스
Composite 패턴의 Main 클래스와 거의 동일하다. 다른 점은 Directory 내용을 표시하기 위해, 표시를 행하는 방문자인 ListVisitor의 인터페이스를 사용한다는 점이다.

Composite 패턴에서는 디렉터리를 표시하기 위해 printList라는 메소드를 사용했다. 이 메소드는 Directory 클래스(즉, 데이터 구조 클래스)에서 구현하였다. 이에 반해 Visitor 패턴에서는 디렉터리를 표시하는 것도 Visitor 클래스에서 실행한다. 디렉터리의 표시도 데이터 구조 안의 각 요소에 대해서 실행하는 처리이기 때문이다.

```java
public class Main {
    public static void main(String[] args) {
        try {
            System.out.println("Making root entries...");
            Directory rootdir = new Directory("root");
            Directory bindir = new Directory("bin");
            Directory tmpdir = new Directory("tmp");
            Directory usrdir = new Directory("usr");
            rootdir.add(bindir);
            rootdir.add(tmpdir);
            rootdir.add(usrdir);
            bindir.add(new File("vi", 10000));
            bindir.add(new File("latex", 20000));
            rootdir.accept(new ListVisitor());

            System.out.println("");
            System.out.println("Making user entries...");
            Directory Kim = new Directory("Kim");
            Directory Lee = new Directory("Lee");
            Directory Park = new Directory("Park");
            usrdir.add(Kim);
            usrdir.add(Lee);
            usrdir.add(Park);
            Kim.add(new File("diary.html", 100));
            Kim.add(new File("Composite.java", 200));
            Lee.add(new File("memo.tex", 300));
            Park.add(new File("game.doc", 400));
            Park.add(new File("junk.mail", 500));
            rootdir.accept(new ListVisitor());
        } catch (FileTreatmentException e) {
            e.printStackTrace();
        }
    }
}
```

### 9) Visitor 쪽과 Element 쪽의 상호 호출

* 예제 프로그램의 시퀀스 다이어그램

![img001](/assets/img/2021-01-14-visitor-pattern/img002.png)

> ① Main 클래스가 ListVisitor의 인스턴스를 만든다.
>
> ② Main은 Directory의 인스턴스에 대해서 accept 메소드를 호출한다.
>
> ③ Directory의 인스턴스는 인수로 전달된 ListVisitor의 visit(Directory) 메소드를 호출한다.
>
> ④ 그러면 ListVisitor의 인스턴스는 그 디렉터리 안을 조사해서 최초의 파일인 accept 메소드를 호출한다. 인수에는 자기 자신(this)을 전달한다.
>
> ⑤ File의 인스턴스는 인수로 전달된 ListVisitor의 visit(File) 메소드를 호출한다. 이때, ListVisitor는 visit(Directory)를 실행 중인 점에 주의해야한다(멀티스레드로서 실행 중 이라는 의미가 아니고 호출이력(콜스택)안에는 visit(Directory)가 있다는 의미이다).
>
> ⑥ visit(File)와 accept에서 반환되면 이번에는 다른 File의 인스턴스(동일한 디렉터리의 2번째 파일)의 accept 메소드를 호출한다. 인수로는 ListVisitor의 인스턴스(this)를 전달한다.
>
> ⑦ 이전과 동일하게 File의 인스턴스는 visit(File)의 메소드를 호출한다. 각 메소드의 처리가 끝나면 호출한 곳으로 점점 되돌아가서 마지막에는 Main의 accept 메소드의 호출로부터도 되돌아온다.

## 4. 개념 확장

### 1) 더블 디스패치(double dispatch)
Visitor 패턴에서 accept 메소드는 element.accept(Visitor), visit 메소드는 visitor.visit(element)로 호출된다. 이 두가지를 비교해 보면 정확히 반대의 관계에 있음을 알 수 있다. element는 visitor를 accept하고, visitor는 element를 visit하고 있다.

Visitor 패턴에서는 ConcreteElement 역할과 ConcreteVisitor 역할을 하는 한 쌍에 의해 실제 처리가 결정된다. 이것을 일반적으로 더블 디스패치(double dispatch: 이중 분리)라고 한다.

### 2) 복잡한 구조
Visitor 패턴의 목적은 처리를 데이터 구조에서 분리하는 일이다. 데이터 구조는 요소를 집합으로 정리하거나, 요소 사이를 연결해주는 중요한 역할을 한다. 그러나 구조를 유지하는 것과 구조를 기초로 한 처리를 기술하는 것과는 별개이다.

### 3) 확장에 대해서는 열고, 수정에 대해서는 닫는다.
기능 확장과 수정에 대한 The Open-Closed Principle(OCP)라는 원칙은 확장은 대환영이지만 기존의 클래스를 수정해서는 안된다. 기존의 클래스를 수정하지 않고 확장할 수 있도록 하는 것이 The Open-Closed Principle의 원칙이다.

클래스를 설계할 때 특별한 이유가 없는 한 확장을 허용해야 한다. 이유 없이 확장을 금지해서는 안되며, 이것이 '확장에 대해서는 열려있다'라는 의미이다.

그러나 확장을 할 때마다 기존의 클래스를 수정해야 하는 것도 곤란하다. 확장을 해도 기존의 클래스는 수정할 필요가 없는 것이 '수정에 대해서는 닫혀있다'라는 의미이다.

클래스에 대한 요구는 빈번하게 변화한다. 그리고 그 요구는 대부분 '기능을 확장하고 싶은' 경우이므로 클래스가 기능 확장을 할 수 없다면 곤란하다. 그러나 한편으로는 이미 완성되어 테스트까지 마친 클래스를 수정한다면 소프트웨어의 품질을 떨어뜨릴 위험이 있다.

확장에 대해서는 열려있고 수정에 대해서는 닫혀있는 클래스가 부품으로써 재이용 가치가 높은 클래스이다. 그리고 디자인 패턴의 목적, 오브젝트(객체)지향의 목적이란 바로 이러한 클래스를 만들 수 있는 구조를 제공하는 것이다.

### 4) ConcreteVisitor 역할의 추가는 간단하다.
새로운 ConcreteVisitor 역할을 추가하는 것은 간단하다. 구체적인 처리는 ConcreteVisitor 역할에 맡길 수 있고, 그 처리를 위한 ConcreteElement 역할을 수정할 필요는 전혀 없기 때문이다.

### 5) ConcreteElement 역할의 추가는 곤란하다.
ConcreteVisitor 역할의 추가는 간단하지만 ConcreteElement 역할의 추가는 곤란하다. 예를 들어, 예제 프로그램에서 Entry 클래스의 하위 클래스로 Device 클래스를 추가한다고 가정한다. Device 클래스는 File 클래스와 Directory 클래스의  형제에 해당한다. 이때 Visitor 클래스에는 visitor(Device) 메소드를 마들 필요성이 생긴다. 그리고 Visitor 클래스의 하위 클래스 전부에 새로운 visitor(Device) 메소드를 구현해야 한다.

### 6) Visitor가 처리하기 위해서 필요한 것
Visitor 패턴에서는 데이터 구조의 요소에 대한 처리를 따로 분리해서 Visitor 역할에게 맡긴다. 이렇게 해서 데이터 구조와 요소에 대한 처리를 분리할 수 있다. 이것은 그럴듯한 말이지만 Element 역할은 Visitor 역할에 대해서 충분한 정보를 공개할 필요가 있다.

예를 들어, 예제 프로그램에서는 visitor(Directory) 안에 각각의 디렉터리 엔트리에 대해 accept를 실행하고 있다. 이와 같은 처리를 실행하기 위해서는 Directory가 '각각의 디렉터리 엔트리를 얻기' 위한 iterator 메소드를 제공할 필요가 있다.

방문자는 데이터 구조에서 필요한 정보를 취득해서 동작한다. 필요한 정보를 얻을 수 없으면 방문자는 제대로 일할 수 없다. 반면에 공개할 필요 없는 정보까지 공개하면 미래의 데이터 구조를 개량하기 어렵게 된다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://en.wikipedia.org/wiki/Visitor_pattern>
* <https://www.slideserve.com/tana-weeks/visitor-pattern>
