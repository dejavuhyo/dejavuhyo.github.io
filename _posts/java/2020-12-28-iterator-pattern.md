--- 
title: Iterator Pattern
author: Hyosik
date: 2020-12-28 09:10:00 +0900
categories: [Language, Java]
tags: [iterator-pattern, iterator, design-pattern, 반복자-패턴, 디자인-패턴]
---

## 1. Iterator 패턴이란
배열 arr의 모든 요소를 표시하기 위해서는 다음과 같이 for문을 사용한다.

```java
for (int i =0; i < arr.length; i++) {
    System.out.println(arr[i]);
}
```

변수 i는 처음에 0으로 초기화되어 1, 2, 3, ...으로 증가한다. 그때마다 arr[i]의 내용이 표시된다. 배열을 요수가 많이 모여있는 것으로, 첨자를 지정하면 많은 요소 중 1개를 선택할 수 있다.

```text
arr[0]: 최초의 요소(0번째 요소)
arr[1]: 그 다음의 요소(1번째 요소)
...
arr[i]: i번째의 요소
...
arr[arr.length - 1]: 최후의 요소
```

for문의 i++에서 i를 하나씩 증가시키면, 현재 주목하고 있는 배열의 요소를 차례대로 처리해 간다. 이처럼 i를 하나씩 증가시키면 배열 arr의 요소 전체를 처음부터 차례대로 검색하게 된다. 여기에서 사용되고 있는 변수 i의 기능을 추상화해서 일반화한 것을 디자인 패턴에서는 _**Iterator 패턴**_ 이라고 한다.

Iterator 패턴이란, 무엇인가 많이 모여있는 것들을 순서대로 지정하면서 전체를 검색하는 처리를 실행하기 위한 것이다. iterator는 무엇인가를 "반복한다"라는 의미이며, 반복자라고도 한다.

## 2. 객체

* Iterator 패턴의 클래스 다이어그램

![img001](/assets/img/2020-12-28-iterator-pattern/img001.png)

### 1) Iterator(반복자)
요소를 순서대로 검색해가는 인터페이스를 결정한다. 다음 요소가 존재하는지를 얻기위한 hasNext()와 다음 요소를 얻기 위한 next()를 결정한다.

### 2) ConcreateIterator(구체적인 반복자)
Iterator가 결정한 인터페이스를 실제고 구현한다. 이 역할은 검색하기 위해 필요한 정보를 가지고 있어야 한다.

### 3) Aggregate(집합체)
Iterator 역할을 만들어내는 인터페이스를 결정한다. 이 인터페이스(API)는 '내가 가지고 있는 요소를 순서대로 검색해 주는 사람'을 만들어내는 메소드이다.

### 4) ConcreateAggregate(구체적인 집합체)
Aggregate 역할이 결정한 인터페이스를 실제로 구현하는 일을 한다. 구체적인 Iterator 역할, 즉 ConcreteIterator 역할의 인스턴스를 만들어낸다.

## 3. 예제

* Agreegate 인터페이스
  - 요소들이 나열되어 있는 '집합체'를 나타낸다.

```java
public interface Aggregate {
    public abstract Iterator iterator();
}
```

* Iterator 인터페이스
  - 요소를 하나씩 나열하면서 루프 변수와 같은 역할을 수행한다.

```java
public interface Iterator {
    public abstract boolean hasNext();
    public abstract Object next();
}
```

* Book 클래스
  - 책 이름을 getName 메소드에서 얻는 일을 한다. 책 이름은 생성자(constructor)에서 인스턴스를 초기화할 때 인수로 지정한다.

```java
public class Book {
    private String name;
    public Book(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
}
```

* BookShelf 클래스
  - 서가를 나타내는 클래스 이며 Aggregate 인테페이스를 구현하고 있다.

```java
public class BookShelf implements Aggregate {
    private Book[] books;
    private int last = 0;
    public BookShelf(int maxsize) {
        this.books = new Book[maxsize];
    }
    public Book getBookAt(int index) {
        return books[index];
    }
    public void appendBook(Book book) {
        this.books[last] = book;
        last++;
    }
    public int getLength() {
        return last;
    }
    public Iterator iterator() {
        return new BookShelfIterator(this);
    }
}
```

* BookShelfIterator 클래스
  - BookShelf 클래스의 검색을 실행하며 Iterator 인터페이스를 구현하고 있다.

```java
public class BookShelfIterator implements Iterator {
    private BookShelf bookShelf;
    private int index;
    public BookShelfIterator(BookShelf bookShelf) {
        this.bookShelf = bookShelf;
        this.index = 0;
    }
    public boolean hasNext() {
        if (index < bookShelf.getLength()) {
            return true;
        } else {
            return false;
        }
    }
    public Object next() {
        Book book = bookShelf.getBookAt(index);
        index++;
        return book;
    }
}
```

* Main 클래스

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        BookShelf bookShelf = new BookShelf(4);
        bookShelf.appendBook(new Book("Around the World in 80 Days"));
        bookShelf.appendBook(new Book("Bible"));
        bookShelf.appendBook(new Book("Cinderella"));
        bookShelf.appendBook(new Book("Daddy-Long-Legs"));
        Iterator it = bookShelf.iterator();
        while (it.hasNext()) {
            Book book = (Book)it.next();
            System.out.println(book.getName());
        }
    }
}
```

## 4. 사용
Iterator를 사용하는 이유는 Iterator를 사용함으로써 구현과 분리해서 하나씩 셀 수 있기 때문이다.

```java
while (it.hasNext()) {
    Book book = (Book)it.next();
    System.out.println(book.getName());
}
```

hasNext와 next라는 Iterator의 메소드만 사용하고 있다. BookShelf의 구현에서 사용되고 있는 메소드는 호출되고 있지 않다. 즉, 위의 while 루프는 BookShelf의 구현에 의존하지 않는다.

BookShelf를 수정하더라도 BookShelf가 iterator 메소드를 가지고 있으며 올바른 Iterator를 반환해 준다면(hasNext와 next 메소드가 올바르게 구현되어 있는 클래스의 인스턴스를 반환해 준다면), 위의 while 루프는 변경하지 않아도 된다.

디자인 패턴은 클래스의 재이용화를 촉진한다. 재이용화를 촉진한다는 것은 클래스를 부품처럼 사용할 수 있게 하고, 하나의 부품을 수정해도 다른 부품에 큰 영향 없이 적은 수정만으로 끝낼 수 있다는 것을 의미한다.

## [출처 및 참고]
* Java 언어로 배우는 디자인 패턴 입문
* <https://itstory.tk/entry/Gof-%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-Iterator-%EB%B0%98%EB%B3%B5%EC%9E%90-%ED%8C%A8%ED%84%B4>
