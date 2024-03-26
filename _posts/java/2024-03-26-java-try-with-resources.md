---
title: Java Try with Resources
author: dejavuhyo
date: 2024-03-26 13:10:00 +0900
categories: [Language, Java]
tags: [java-try, try-with-resources, try-catch, try-catch-finally]
---

## 1. try-with-resources 사용
간단히 말해서, auto-closed하면 리소스가 try 내부에서 선언되고 초기화되어야 한다.

```java
try (PrintWriter writer = new PrintWriter(new File("test.txt"))) {
    writer.println("Hello World");
```

## 2. try–catch-finally를 try-with-resources로 교체
새로운 리소스 사용 시도 기능을 사용하는 간단하고 확실한 방법은 기존의 장황한 `try-catch-finally` 블록을 대체하는 것이다.

다음 코드 샘플을 비교해 본다.

첫 번째는 일반적인 `try-catch-finally` 블록이다.

```java
Scanner scanner = null;
try {
    scanner = new Scanner(new File("test.txt"));
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {
        scanner.close();
    }
}
```

try-with-resources를 사용하는 매우 간결한 새로운 솔루션은 다음과 같다.

```java
try (Scanner scanner = new Scanner(new File("test.txt"))) {
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException fnfe) {
    fnfe.printStackTrace();
}
```

## 3. 여러 리소스 try-with-resources
세미콜론으로 구분하여 `try-with-resources` 블록에서 여러 리소스를 선언할 수 있다.

```java
try (Scanner scanner = new Scanner(new File("testRead.txt"));
    PrintWriter writer = new PrintWriter(new File("testWrite.txt"))) {
    while (scanner.hasNext()) {
	writer.print(scanner.nextLine());
    }
}
```

## 4. AutoCloseable를 사용하는 사용자 정의 리소스
`try-with-resources` 블록에 의해 올바르게 처리될 사용자 정의 리소스를 생성하려면 클래스는 Closeable 또는 AutoCloseable 인터페이스를 구현하고 close 메서드를 재정의해야 한다.

```java
public class MyResource implements AutoCloseable {
    @Override
    public void close() throws Exception {
        System.out.println("Closed MyResource");
    }
}
```

## 5. 리소스 Closing Order
먼저 정의/획득된 리소스는 마지막에 닫힌다. 이 동작의 예이다.

* Resource 1

```java
public class AutoCloseableResourcesFirst implements AutoCloseable {

    public AutoCloseableResourcesFirst() {
        System.out.println("Constructor -&gt; AutoCloseableResources_First");
    }

    public void doSomething() {
        System.out.println("Something -&gt; AutoCloseableResources_First");
    }

    @Override
    public void close() throws Exception {
        System.out.println("Closed AutoCloseableResources_First");
    }
}
```

* Resource 2

```java
public class AutoCloseableResourcesSecond implements AutoCloseable {

    public AutoCloseableResourcesSecond() {
        System.out.println("Constructor -&gt; AutoCloseableResources_Second");
    }

    public void doSomething() {
        System.out.println("Something -&gt; AutoCloseableResources_Second");
    }

    @Override
    public void close() throws Exception {
        System.out.println("Closed AutoCloseableResources_Second");
    }
}
```

* Code

```java
private void orderOfClosingResources() throws Exception {
    try (AutoCloseableResourcesFirst af = new AutoCloseableResourcesFirst();
        AutoCloseableResourcesSecond as = new AutoCloseableResourcesSecond()) {

        af.doSomething();
        as.doSomething();
    }
}
```

* Output

```text
Constructor -> AutoCloseableResources_First
Constructor -> AutoCloseableResources_Second
Something -> AutoCloseableResources_First
Something -> AutoCloseableResources_Second
Closed AutoCloseableResources_Second
Closed AutoCloseableResources_First
```

## 6. catch 그리고 finally
`try-with-resources` 블록은 여전히 ​​catch 및 finally 블록을 가질 수 있으며 이는 기존 try 블록과 동일한 방식으로 작동한다.

## 7. Java 9 효과적인 최종 변수
Java 9 이전에는 `try-with-resources` 블록 내에서만 새로운 변수를 사용할 수 있었다.

```java
try (Scanner scanner = new Scanner(new File("testRead.txt")); 
    PrintWriter writer = new PrintWriter(new File("testWrite.txt"))) { 
    // omitted
}
```

위에 표시된 것처럼 이는 여러 리소스를 선언할 때 특히 장황했다. Java 9 및 [JEP 213](https://openjdk.org/jeps/213)의 일부로 이제 `try-with-resources` 블록 내에서 final 또는 심지어 효과적으로 final 변수를 사용할 수 있다.

```java
final Scanner scanner = new Scanner(new File("testRead.txt"));
PrintWriter writer = new PrintWriter(new File("testWrite.txt"))
try (scanner;writer) { 
    // omitted
}
```

간단히 말해서, 명시적으로 final로 표시되지 않은 경우에도 첫 번째 할당 후 변경되지 않으면 변수는 사실상 final 이다.

위에 표시된 것처럼 scanner 변수는 명시적으로 final로 선언되므로 `try-with-resources` 블록과 함께 사용할 수 있다. writer 변수는 명시적으로 최종 변수는 아니지만 첫 번째 할당 이후에는 변경되지 않는다. 따라서 writer 변수도 사용할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-try-with-resources](https://www.baeldung.com/java-try-with-resources)
