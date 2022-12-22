---
title: Composite Pattern
author: dejavuhyo
date: 2021-01-12 10:30:00 +0900
categories: [Language, DesignPattern]
tags: [composite-pattern, composite, design-pattern, 컴포지트-패턴, 디자인-패턴]
---

## 1. Composite 패턴이란
컴퓨터의 파일 시스템에는 '디렉터리'라는 것이 있다. 디렉터리 안에는 파일이 있거나 다른 디렉터리(하위 디렉터리)가 있기도 한다. 또 그 하위 디렉터리 안에는 다른 파일이나 하위 디렉터리가 있기도 한다. 디렉터리는 이와 같이 '상자 안의 상자'로 된 구조, 즉 재귀적인 구조를 만들어 낸다.

디렉터리와 파일은 서로 다르지만 모두 '디렉터리 안에 넣을 수 있는 것'이다. 이렉터리와 파일을 합해서 '디렉터리 엔트리'라고 부르기도 한다. 디렉터리 엔트리라는 이름으로 디렉터리와 파일을 같은 종류로 간주하고(동일시하고) 있다.

디렉터리와 파일을 모아서 디렉터리 엔트리로 취급하듯이 그릇과 내용물을 같은 종류로 취급하면 편리한 경우가 있다. 그릇 안에는 내용물을 넣을 수도 있고 더욱 작은 그릇을 넣을 수도 있다. 그리고 그 작은 그릇 안에 더 작은 그릇을 넣고... 이런 식으로 '상자 안의 상자' 처럼 된 구조, 즉 재귀적인 구조를 만들 수 있다. _**Composite 패턴**_ 은 그릇과 내용물을 동일시해서 재귀적인 구조를 만들기 위한 디자인 패턴이다.

## 2. 객체

* Composite 패턴의 클래스 다이어그램

![img001](/assets/img/2021-01-12-composite-pattern/img001.png)

### 1) Leaf(나뭇잎)의 역할
Leaf는 '내용물'을 표시하는 역할을 하며 내부에는 다른 것을 넣을 수 없다.

### 2) Composite(복합체)의 역할
Composite는 '그릇'을 나타내는 역할을 하며 Leaf 역할이나 Composite 역할을 넣을 수 있다.

### 3) Component의 역할
Leaf 역할과 Composite 역할을 동일시하는 역할을 한다. Component는 Leaf 역할과 Composite 역할에 공통적인 상위 클래스로 실현한다.

### 4) Client(의뢰자)의 역할
Composite 패턴의 사용자이다. Composite 역할이 포함하고 있는 Component 역할(즉, Leaf 역할이나 Composite 역할)을 부모에 대한 '자식'으로 간주한다. getChild 메소드는 Component 역할로부터 '자식'을 얻기 위한 메소드이다.

## 3. 예제

### 1) Entry 클래스
추상  클래스로서 디렉터리 엔트리를 표현하며 하위 클래스인 File 클래스와 Directory 클래스를 만든다.

디렉터리 엔트리는 이름을 가지고 있다. 이름을 얻기 위한 메소드로서 getName을 준비하며 하위 클래스에서 구현을 한다. 또한 디렉터리 엔트리는 크기를 가지고 있다. 크기를 얻기 위한 getSize 메소드를 준비하며 이것도 하위 클래스에서 구현한다.

```java
public abstract class Entry {
    public abstract String getName();
    public abstract int getSize();
    public Entry add(Entry entry) throws FileTreatmentException {
        throw new FileTreatmentException();
    }
    public void printList() {
        printList("");
    }
    protected abstract void printList(String prefix);
    public String toString() {
        return getName() + " (" + getSize() + ")";
    }
}
```

### 2) File 클래스
파일을 표현하는 클래스이다. Entry 클래스의 하위 클래스로 선언한다. 필드는 두 개이며 파일의 이름을 표현하는 name과 크기를 나타내는 size이다. 생성자에서는 이름과 크기를 제공해서 File의 인스턴스를 만든다.

추상 메소드는 상위 클래스의 Entry 클래스에서 모두 구현되고 있으므로 File 클래스는 추상 클래스가 아니다.

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
    protected void printList(String prefix) {
        System.out.println(prefix + "/" + this);
    }
}
```

### 3) Directory 클래스
디렉터리를 표현하는 클래스이며 Entry 클래스의 하위 클래스로 정의되어 있다.

필드는 두 개가 있다. 첫 번째의 필드 name은 디렉터리의 이름을 나타내며 File 클래스와 동일하다. 그러나 Directory에는 크기를 나타내는 필드가 없다. 이것은 디렉터리의 크기를 동적으로 계산해서 구하고 있기 때문이다. 두 번째의 필드 directory는 ArrayList로 정의되고 있다. 이 directory가 디렉터리 엔트리를 저장해 두기 위한 필드이다.

```java
import java.util.Iterator;
import java.util.ArrayList;

public class Directory extends Entry {
    private String name; // 디렉터리의 이름
    private ArrayList directory = new ArrayList(); // 디렉터리 엔트리의 집합
    public Directory(String name) { // 생성자
        this.name = name;
    }
    public String getName() { // 이름을 얻는다
        return name;
    }
    public int getSize() { // 크기를 얻는다
        int size = 0;
        Iterator it = directory.iterator();
        while (it.hasNext()) {
            Entry entry = (Entry)it.next();
            size += entry.getSize();
        }
        return size;
    }
    public Entry add(Entry entry) { // 엔트리의 추가
        directory.add(entry);
        return this;
    }
    protected void printList(String prefix) { // 엔트리의 일람
        System.out.println(prefix + "/" + this);
        Iterator it = directory.iterator();
        while (it.hasNext()) {
            Entry entry = (Entry)it.next();
            entry.printList(prefix + "/" + name);
        }
    }
}
```

### 4) FileTreatmentException 클래스
파일에 대해서 add 메소드를 잘못 호출 했을 때 제공되는 예외이다. 이 예외는 Java의 클래스 라이브러리에서 제공하는 것이 아니라 이 예제 프로그램을 위해 정의한 클래스이다.

```java
public class FileTreatmentException extends RuntimeException {
    public FileTreatmentException() {
    }
    public FileTreatmentException(String msg) {
        super(msg);
    }
}
```

### 5) Main 클래스
디렉터리 계층을 만든다. 우선 처음에 root, bin, tmp, usr이라는 디렉터리를 만들고 bin의 아래에 vi라는 파일과 latex라는 파일을 넣는다. 그리고 usr 디렉터리 아래에 Kim, Lee, Park라는 디렉터리를 만들어 각 사람의 파일을 그 아래에 만든다.

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
            rootdir.printList();

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
            rootdir.printList();
        } catch (FileTreatmentException e) {
            e.printStackTrace();
        }
    }
}
```

## 4. 개념 확장

### 1) 복수와 단수의 동일시
Composite 패턴은 그릇과 내용물을 동일시하는 패턴이지만, 복수와 단수 역시 동일시 한다고 할 수 있다. 즉, 여러개를 모아서 마치 하나인 것처럼 취급하는 것이다.

### 2) add 메소드의 구현 방법
add 메소드를 실제로 사용할 수 있는 것은 Directory 클래스뿐이다. add 메소드의 설치와 구현에는 다양한 방법이 있다.

* 경우1: Entry 클래스에서 구현하고 에러로 처리한다.
  - 예제 프로그램에서는 add 메소드를 Entry 클레스에서 구현하고 에러로 처리했다. add 메소드를 실제로 사용할 수 있는 Directory 클래스에서는 Entry 클래스의 add를 오버라이드해서 의미있는 구현으로 바꿔 놓는다. File 클래스는 Entry 클래스에서 add 메소드를 상속하고 있기 때문에 add는 가능하지만 예외가 제공된다.

* 경우2: Entry 클래스에서 구현하고 아무것도 실행하지 않는다.
  - add 메소드는 Entry 클래스에서 구현하지만 에러로 처리하지 않는(아무것도 실행하지 않는) 방법도 있다.

* 경우3: Entry 클래스에서 선언은 하지만 구현은 하지 않는다.
  - add 메소드를 Entry 클래스에서는 추상 메소드로 하고, 하위 클래스에서는 필요하면 정의하고 필요하지 않으면 에러로 처리하는 방법도 있다. 이 방법은 이로간성을 가질 수 있고 불필요한 경우의 작동을 하위 클래스에서 결정할 수 있는 장점이 있다. 그러나 원래 필요 없는 add(경우에 따라서는 remove나 getChild 등)를 File 쪽에서 정의해야 하는 단점도 있다.

* 경우4: Directory 클래스에만 넣는다.
  - add 메소드를 Entry 클래스에는 넣지 않고, 처음부터 정말로 필요한 Directory 클래스에 넣는 방법도 있다. 그러나 이 방법은 Entry 형의 변수(실제의 내용은 Directory의 인스턴스)에 add할 때, 하나하나 Directory 형으로 캐스트해야하는 불편함이 있다.

### 3) 재귀적 구조는 모든 장면에서 등장한다.
프로그래밍을 할 때 재귀적인 구조 및 Composite 패턴은 여러 장면에서 등장한다. 예를 들면, 윈도우 시스템에서는 윈도우 안에 자식 윈도우를 가지게 하는데 이것이 전형적인 Composite 패턴이다.

문장의 개별 항목 안에 다시 항목이 포함되어 있는 것도 재귀적인 구조라고 할 수 있다. 컴퓨터에 대한 커맨드(명령)를 정리한 매크로 커맨드를 만들 때, 그 매크로 커맨드를 재귀적인 구조로 실현하면 매크로 커맨트도 만들 수 있다. 일반적으로 트리 구조로 된 데이터 구조는 Composite 패턴에 해당된다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* [https://en.wikipedia.org/wiki/Composite_pattern](https://en.wikipedia.org/wiki/Composite_pattern)
