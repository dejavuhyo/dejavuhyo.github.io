---
title: Java 메서드 참조
author: dejavuhyo
date: 2023-01-25 21:05:00 +0900
categories: [Language, Java]
tags: [java-method-references, method-references, 자바-메서드-참조, 메서드-참조, 자바-메서드]
---

## 1. 메서드 참조
메서드 참조는 이름으로 메서드를 가리키는 데 도움이 된다. 메서드 참조는 `::` 기호를 사용하여 설명한다.

메서드 참조는 다음 유형의 메서드를 가리키는 데 사용할 수 있다.

* 정적 메서드

* 인스턴스 메서드

* new 연산자를 사용하는 생성자 (`TreeSet::new`)

## 2. Method Reference Example

* JavaTest.java

```java
import java.util.ArrayList;
import java.util.List;

public class JavaTest {
    public static void main(String args[]) {
        List names = new ArrayList();

        names.add("Mahesh");
        names.add("Suresh");
        names.add("Ramesh");
        names.add("Naresh");
        names.add("Kalpesh");

        names.forEach(System.out::println);
    }
}
```

* 결과

```text
Mahesh
Suresh
Ramesh
Naresh
Kalpesh
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/java8/java8_method_references.htm](https://www.tutorialspoint.com/java8/java8_method_references.htm)
