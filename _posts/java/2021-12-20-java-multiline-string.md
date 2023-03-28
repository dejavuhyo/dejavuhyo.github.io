---
title: Java Multiline String
author: dejavuhyo
date: 2021-12-20 12:20:00 +0900
categories: [Language, Java]
tags: [java-multiline-string, multiline-string, java-string, text-blocks, java-여러-줄-문자열, 여러-줄-문자열, java-문자열, 텍스트-블록]
---

## 1. 여러 줄 문자열
Java에서 여러 줄 문자열을 선언하는 방법이다.

Java 15에서 텍스트 블록이라는 새로운 기본 기능을 사용할 수 있다.

## 2. 텍스트 블록
문자열을 `"""`(큰따옴표 3개)로 선언하여 텍스트 블록을 사용할 수 있다.

```java
public String textBlocks() {
    return """
        Get busy living
        or
        get busy dying.
        --Stephen King""";
}
```

그것은, 단연코, 멀티 라인 문자열을 선언하는 가장 편리한 방법이다. 줄 구분 기호나 들여쓰기 공백을 다룰 필요가 없다.

이 기능은 Java 15에서 사용할 수 있지만 미리보기 기능을 활성화 하면 Java 13 및 14에서도 사용할 수 있다.

## 3. 줄 구분 기호 얻기
각 운영 체제는 새 라인을 정의하고 인식하는 고유한 방법을 가질 수 있다. Java에서는 운영 체제 줄 구분 기호를 얻는 것이 매우 쉽다.

```java
String newLine = System.getProperty("line.separator");
```

## 4. 문자열 연결
문자열 연결은 여러 줄 문자열을 만드는데 사용할 수 있는 쉬운 기본 방법이다.

```java
public String stringConcatenation() {
    return "Get busy living"
            .concat(newLine)
            .concat("or")
            .concat(newLine)
            .concat("get busy dying.")
            .concat(newLine)
            .concat("--Stephen King");
}
```

`+` 연산자를 사용하는 것은 동일한 결과를 얻는 또 다른 방법이다. Java 컴파일러는 concat() 및 `+` 연산자를 같은 방식으로 번역한다.

```java
public String stringConcatenation() {
    return "Get busy living"
            + newLine
            + "or"
            + newLine
            + "get busy dying."
            + newLine
            + "--Stephen King";
}
```

## 5. String Join
Java 8은 일부 문자열과 함께 구분 기호를 인수로 사용하는 [String#join](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html#join(java.lang.CharSequence,java.lang.CharSequence...))을 도입했다. 모든 입력 문자열이 구분 기호와 함께 결합된 최종 문자열을 반환한다.

```java
public String stringJoin() {
    return String.join(newLine,
                       "Get busy living",
                       "or",
                       "get busy dying.",
                       "--Stephen King");
}
```

## 6. String Builder
StringBuilder는 문자열을 빌드하기 위한 도우미 클래스이다. StringBuilder는 StringBuffer를 대체하기 위해 자바 1.5에 도입되었다. 루프에 거대한 문자열을 구축하는데 좋은 선택이다.

```java
public String stringBuilder() {
    return new StringBuilder()
            .append("Get busy living")
            .append(newLine)
            .append("or")
            .append(newLine)
            .append("get busy dying.")
            .append(newLine)
            .append("--Stephen King")
            .toString();
}
```

## 7. String Writer
StringWriter는 여러 줄 문자열을 만드는데 사용할 수 있는 또 다른 방법이다. PrintWriter를 사용하기 때문에 여기서는 newLine이 필요하지 않다. println 함수는 자동으로 새 줄을 추가한다.

```java
public String stringWriter() {
    StringWriter stringWriter = new StringWriter();
    PrintWriter printWriter = new PrintWriter(stringWriter);
    printWriter.println("Get busy living");
    printWriter.println("or");
    printWriter.println("get busy dying.");
    printWriter.println("--Stephen King");
    return stringWriter.toString();
}
```

## 8. Guava Joiner
이와 같은 간단한 작업을 위해 외부 라이브러리를 사용하는 것은 의미가 없지만 프로젝트에서 이미 다른 용도로 라이브러리를 사용하고 있다면 활용할 수 있다. 예를 들어 Google의 Guava 라이브러리는 매우 유명하다. Guava에는 여러 줄 문자열을 작성할 수 있는 Joiner 클래스가 있다.

```java
public String guavaJoiner() {
    return Joiner.on(newLine).join(ImmutableList.of("Get busy living",
        "or",
        "get busy dying.",
        "--Stephen King"));
}
```

## 9. 파일에서 불러오기
Java는 파일을 있는 그대로 읽는다. 즉, 텍스트 파일에 여러 줄의 문자열이 있는 경우 파일을 읽을 때 동일한 문자열을 갖게 된다. Java에서 파일 을 읽는 방법에는 여러 가지가 있다.

실제로 코드에서 긴 문자열을 분리하는 것이 좋다.

```java
public String loadFromFile() throws IOException {
    return new String(Files.readAllBytes(Paths.get("src/main/resources/stephenking.txt")));
}
```

## 10 .IDE 기능 사용
많은 최신 IDE는 여러 줄 복사/붙여넣기를 지원한다. Eclipse 및 IntelliJ IDEA가 이러한 IDE의 예이다. 우리는 단순히 여러 줄 문자열을 복사하여 이러한 IDE에서 두 개의 큰따옴표 안에 붙여넣을 수 있다.

분명히 이 방법은 런타임에 문자열을 생성할 때는 작동하지 않지만 여러 줄 문자열을 얻는 빠르고 쉬운 방법이다.

## [출처 및 참고]
* [https://www.baeldung.com/java-multiline-string](https://www.baeldung.com/java-multiline-string)
