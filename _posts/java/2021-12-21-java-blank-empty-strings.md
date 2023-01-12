---
title: Java Empty or Blank 문자열 확인
author: dejavuhyo
date: 2021-12-21 09:25:00 +0900
categories: [Language, Java]
tags: [java-blank-strings, java-empty-strings, java-blank-empty, blank-empty-strings, 자바-empty-문자열, 자바-blank-문자열, 자바-빈-문자열]
---

## 1. Empty vs. Blank
문자열이 empty나 blank인 경우를 아는 것은 매우 일반적이지만 정의와 함께 동일한 위치에 있는지 확인한다.

문자열이 null이거나 길이가 없는 문자열이면 비어있는 것으로 간주한다. 문자열이 공백으로만 구성되어 있으면 blank라고 합니다.

Java의 경우 whitespaces는 spaces, 탭 등과 같은 문자이다.

## 2. Empty Strings

### 1) Java 6 이상 사용
최소한 Java 6을 사용 중이라면 빈 문자열을 확인하는 가장 간단한 방법은 `String#isEmpty`이다.

```java
boolean isEmptyString(String string) {
    return string.isEmpty();
}
```

또한 null-safe를 위해, 추가적인 검사가 필요하다.

```java
boolean isEmptyString(String string) {
    return string == null || string.isEmpty();
}
```

### 2) Java 5 이하
`String#isEmpty`는 Java 6과 함께 도입되었다. Java 5 이하에서는 `String#length`를 대신 사용할 수 있다.

```java
boolean isEmptyString(String string) {
    return string == null || string.length() == 0;
}
```

사실 `String#isEmpty`는 `String#length`의 바로 가기일 뿐이다.

## 3. Blank Strings
`String#isEmpty` 및 `String#length` 모두 빈 문자열을 확인하는데 사용할 수 있다.

빈 문자열도 감지 하려면 `String#trim`을 사용하여 이를 수행할 수 있다. 검사를 수행하기 전에 모든 선행 및 후행 공백을 제거한다.

```java
boolean isBlankString(String string) {
    return string == null || string.trim().isEmpty();
}
```

정확히 말하면, `String#trim`은 `U+0020` 이하의 유니코드 코드를 가진 모든 선행 및 후행 문자를 제거한다.

또한 String은 변경할 수 없으므로 trim을 호출해도 실제로 기본 문자열이 변경되지는 않는다.

위의 접근 방식 외에도 Java 11부터 트리밍 대신 isBlank() 메서드를 사용할 수도 있다.

```java
boolean isBlankString(String string) {
    return string == null || string.isBlank();
}
```

`isBlank()` 메소드는 힙(heap)에 새로운 문자열을 생성하지 않으므로 조금 더 효율적이다. 그렇기 때문에 Java 11 이상을 사용하는 경우 이 방법이 선호된다.

## 4. Bean Validation
빈 문자열을 확인하는 또 다른 방법은 정규식이다. 예를 들어 Java Bean Validation을 사용하면 다음과 같은 이점이 있다.

```java
@Pattern(regexp = "\\A(?!\\s*\\Z).+")
String someString;
```

지정된 정규식은 비어 있거나 비어 있는 문자열의 유효성을 검사하지 않는다.

## 5. Apache Commons
종속성을 추가하는 것이 괜찮다면, [Apache Commons Lang](https://commons.apache.org/proper/commons-lang/)을 사용할 수 있다. 이것은 자바를 위한 많은 도우미를 가지고 있다.

Maven을 사용할 경우 [commons-lang3](https://mvnrepository.com/artifact/org.apache.commons/commons-lang3) 의존성을 pom에 추가해야 한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```

무엇보다도, 이것은 StringUtils를 제공한다.

이 클래스에는 isEmpty, isBlank 등의 메서드가 제공된다.

```java
StringUtils.isBlank(string)
```

이 호출은 isBlankString 메서드와 동일하다. null-safe하고 whitespaces도 확인한다.

## 6. Guava
특정 문자열 관련 유틸리티를 제공하는 또 다른 잘 알려진 라이브러리는 Google의 Guava 이다. 버전 23.1부터 Guava에는 android 및 jre의 두 가지 버전이 있다. Android 버전은 Android 및 Java 7을 대상으로 하는 반면 JRE 버전은 Java 8을 대상으로 한다.

Android를 대상으로 하지 않는 경우 pom에 [JRE flavor](https://mvnrepository.com/artifact/com.google.guava/guava)를 추가 하면 된다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.0.1-jre</version>
</dependency>
```

Guavas Strings 클래스는 [Strings.isNullOrEmpty](https://guava.dev/releases/27.1-jre/api/docs/com/google/common/base/Strings.html#isNullOrEmpty-java.lang.String-) 메소드와 함께 제공된다.

```java
Strings.isNullOrEmpty(string)
```

지정된 문자열이 null인지 empty인지는 확인하지만 whitespace-only 문자열은 확인하지 않는다.

> 가장 편리한 방법은 StringUtils.isBlank와 같은 도우미를 제공하는 Apache Commons Lang을 사용하는 것이다. 일반 Java를 고수하고 싶다면 `String#trim`과 `String#isEmpty` 또는 `String#length` 조합을 사용할 수 있다. Bean 유효성 검사의 경우 정규식을 대신 사용할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-blank-empty-strings](https://www.baeldung.com/java-blank-empty-strings)
