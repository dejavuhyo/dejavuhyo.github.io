---
title: 문자열의 마지막 문자 제거
author: dejavuhyo
date: 2021-12-23 09:30:00 +0900
categories: [Language, Java]
tags: [java-remove-character, remove-last-character, remove-character, java-string, java-substring, java-chop, java-replaceall, 자바-문자-제거, 문자-제거]
---

## 1. String.substring() 사용
가장 쉬운 방법은 String 클래스의 내장 substring() 메서드를 사용하는 것 이다.

주어진 문자열의 마지막 문자를 제거하려면 시작 인덱스로 0과 끝에서 두 번째 문자 인덱스라는 두 개의 매개변수를 사용해야 한다. String의 length() 메서드를 호출 하고 결과에서 1을 빼서 이를 달성할 수 있다.

그러나 이 방법은 null-safe가 아니며 빈 문자열을 사용하면 실패한다.

null 및 빈 문자열 문제를 극복하기 위해 메서드를 도우미 클래스로 래핑할 수 있다.

```java
public static String removeLastChar(String s) {
    return (s == null || s.length() == 0)
      ? null 
      : (s.substring(0, s.length() - 1));
}
```

코드를 리팩토링하여 Java 8을 사용할 수 있다.

```java
public static String removeLastCharOptional(String s) {
    return Optional.ofNullable(s)
      .filter(str -> str.length() != 0)
      .map(str -> str.substring(0, str.length() - 1))
      .orElse(s);
    }
```

## 2. StringUtils.substring() 사용
유용한 String 작업을 제공하는 Apache Commons Lang3 라이브러리의 StringUtils 클래스를 사용할 수 있다. 그 중 하나는 예외를 처리 하는 null-safe substring() 메서드 이다.

StringUtils를 포함하려면 pom.xml 파일을 업데이트해야 한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```

```StringUtils.substring()```에는 주어진 문자열, 첫 번째 문자의 인덱스(이 경우 항상 0이 됨) 및 끝에서 두 번째 문자의 인덱스라는 세 가지 매개변수가 필요하다. 단순히 length() 메서드를 사용하여 1을 뺄 수 있다.

```java
String TEST_STRING = "abcdef";

StringUtils.substring(TEST_STRING, 0, TEST_STRING.length() - 1);
```

그러나 이 작업은 다시 null-safe가 아니다. 빈 문자열에서도 잘 작동 한다.

## 3. StringUtils.chop() 사용
StringUtils 클래스는 모든 에지 시나리오(empty 및 null Strings)에서 잘 작동 하는 chop() 메서드를 제공한다.

사용하기 매우 쉬우며 문자열이라는 매개 변수만 필요한다. 그것의 유일한 목적은 마지막 캐릭터를 제거하는 것이다. 그 이상도 이하도 아니다.

```java
StringUtils.chop(TEST_STRING);
```

## 4. 정규 표현식 사용
정규 표현식을 잘 활용 하여 문자열에서 마지막 문자(또는 임의의 수의 문자)를 제거할 수도 있다.

예를 들어, String 클래스 자체의 replaceAll() 메서드를 사용할 수 있다. 이 메서드는 정규식과 교체 String의 두 매개변수를 사용한다.

```java
TEST_STRING.replaceAll(".$", "");
```

String에서 메서드를 호출하고 있기 때문에 작업은 물론 null-safe가 아니다.

또한, replaceAll() 및 regex 표현식은 첫눈에 복잡할 수 있다. 사용자 친화적으로 만들기 위해 도우미 클래스로 래핑할 수 있다.

```java
public static String removeLastCharRegex(String s) {
    return (s == null) ? null : s.replaceAll(".$", "");
}
```

만약 문자열이 새로운 라인으로 끝난다면 regex의 "."는 줄 터미네이터를 제외한 모든 문자와 일치하기 때문에 위의 메소드는 실패할 것이다.

마지막으로 Java 8로 구현을 다시 한다.

```java
public static String removeLastCharRegexOptional(String s) {
    return Optional.ofNullable(s)
      .map(str -> str.replaceAll(".$", ""))
      .orElse(s);
}
```

## [출처 및 참고]
* <https://www.baeldung.com/java-remove-last-character-of-string>
