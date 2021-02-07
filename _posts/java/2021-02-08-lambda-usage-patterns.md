---
title: 람다의 활용 패턴
author: dejavuhyo
date: 2021-02-08 06:30:00 +0900
categories: [Language, Java]
tags: [lambda-usage-patterns, usage-patterns, lambda, 람다-활용-패턴, 람다]
---

## 1. 실행 어라운드 패턴
자원 처리(예를 들면 데이터베이스의 파일 처리)에 사용하는 순환 패턴(recurrent pattern)은 자원을 열고, 처리한 다음에, 자원을 닫는 순서로 이루어진다. 설정(setup)과 정리(cleanup) 과정은 대부분 비슷하다. 즉, 실제 자원을 처리하는 코드를 설정과 정리 두 과젖이 둘러싸는 형태를 갖는다. 아래와 같은 형식의 코드를 실행 어라운드 패턴(execute around pattern)이라고 부른다. 예제는 파일에서 한 행을 읽는 코드다(예제는 자바 7에 새로 추가된 try-with-resources 구문을 사용했다. 이를 사용하면 자원을 명시적으로 닫을 필요가ㅐ 없으므로 간결한 코드를 구현하는 데 도움을 준다).

```java
public String processFile() throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return br.readLine(); // 실제 필요한 작업을 하는 행이다.
    }
}
```

## 2. 1단계: 동작 파라미터화를 기억하라
현재 코드는 파일에서 한 번에 한 줄만 읽을 수 있다. 한 번에 두줄을 읽거나 가장 자주 사용되는 단어를 반환하려면, 기존의 설정, 정리 과정은 재사용하고 processFile 메서드만 다른 동작을 수행하도록 명령할 수 있다면 좋을 것이다. processFile의 동작을 파라미터화하는 것이다. processFile 메서드가 BufferedReader를 이용해서 다른 동작을 수행할 수 있도록 processFile 메서드로 동작을 전달해야 한다.

람다를 이용해서 동작을 전달할 수 있다. processFile 메서드가 한 번에 두 행을 읽게 하려면 BufferedReader를 인수로 받아서 String을 반환하는 람다가 필요하다. 다음은 BufferedReader에서 두 행을 출락하는 코드다.

```java
String result = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

## 3. 2단계: 함수형 인터페이스를 이용해서 동작 전달
함수형 인터페이스 자리에 람다를 사용할 수 있다. 따라서 BufferedReader -> String과 IOException을 던질(throw) 수 있는 시그니처와 일치하는 함수형 인터페이스를 만들어야 한다. 이 인터페이스를 BufferedReaderProcessor라고 정의하자.

```java
@FunctionalInterface
public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException;
}
```

정의한 인터페이스를 processFile 메서드의 인수로 전달할 수 있다.

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
    ...
}
```

## 4. 3단계: 동작 실행
BufferedReaderProcessor에 정의된 process 메서드의 시그니처(BufferedReader -> String)와 일치하는 람다를 전달할 수 있다. 람다의 코드가 processFile 내부에서 람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으며 전달된 코드는 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리한다. 따라서 processFile 바디 내에서 BufferedReaderProcessor 객체의 process를 호출할 수 있다.

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return p.process(br); // BufferedReader 객체 처리
    }
}
```

## 5. 4단계: 람다 전달
람다를 이용해서 다양한 동작을 processFile 메서드로 전달할 수 있다.

다음은 한 행을 처리하는 코드다.

```java
String oneLine= processFile((BufferedReader br) -> br.readLine());
```

다음은 두 행을 처리하는 코드다.

```java
String twoLines = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

아래는 processFile 메서드를 더 유연하게 만드는 과정을 보여준다.

* 실행 어라운드 패턴을 적용하는 네 단계의 과정

```java
// 1단계
public String processFile() throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return br.readLine();
    }
}

// 2단계
public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException;
}
public String processFile(BufferedReaderProcessor p) throws IOException {
    ...
}

// 3단계
public String processFile(BufferedReaderProcessor p) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return p.process(br);
    }
}

// 4단계
String oneLine= processFile((BufferedReader br) -> br.readLine());
String twoLines = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

## [출처 및 참고]
* Modern Java in Action(모던 자바 인 액션)
