---
title: Java 무작위 문자열 생성
author: dejavuhyo
date: 2021-12-22 09:30:00 +0900
categories: [Language, Java]
tags: [java-random-string, random-string, 자바-무작위-문자열, 무작위-문자열, 임의의-문자열]
---

## 1. 일반 Java를 사용하여 무한한 무작위 문자열 생성
7자로 제한 되는 임의의 문자열을 생성한다.

```java
@Test
public void givenUsingPlainJava_whenGeneratingRandomStringUnbounded_thenCorrect() {
    byte[] array = new byte[7]; // length is bounded by 7
    new Random().nextBytes(array);
    String generatedString = new String(array, Charset.forName("UTF-8"));

    System.out.println(generatedString);
}
```

새 문자열은 원격으로 영숫자가 될 수 없다.

## 2. 일반 Java로 임의의 경계 문자열 생성
더 제한된 임의의 문자열을 만드는 방법이다. 소문자 알파벳 문자와 설정된 길이를 사용하여 임의의 문자열을 생성한다.

```java
@Test
public void givenUsingPlainJava_whenGeneratingRandomStringBounded_thenCorrect() {
 
    int leftLimit = 97; // letter 'a'
    int rightLimit = 122; // letter 'z'
    int targetStringLength = 10;
    Random random = new Random();
    StringBuilder buffer = new StringBuilder(targetStringLength);
    for (int i = 0; i < targetStringLength; i++) {
        int randomLimitedInt = leftLimit + (int) 
          (random.nextFloat() * (rightLimit - leftLimit + 1));
        buffer.append((char) randomLimitedInt);
    }
    String generatedString = buffer.toString();

    System.out.println(generatedString);
}
```

## 3. Java 8을 사용하여 임의의 알파벳 문자열 생성
JDK 8에 추가된 Random.ints 를 사용하여 알파벳 문자열을 생성한다.

```java
@Test
public void givenUsingJava8_whenGeneratingRandomAlphabeticString_thenCorrect() {
    int leftLimit = 97; // letter 'a'
    int rightLimit = 122; // letter 'z'
    int targetStringLength = 10;
    Random random = new Random();

    String generatedString = random.ints(leftLimit, rightLimit + 1)
      .limit(targetStringLength)
      .collect(StringBuilder::new, StringBuilder::appendCodePoint, StringBuilder::append)
      .toString();

    System.out.println(generatedString);
}
```

## 4. Java 8로 임의의 영숫자 문자열 생성
영숫자 문자열을 얻기 위해 문자 집합을 확장할 수 있다.

```java
@Test
public void givenUsingJava8_whenGeneratingRandomAlphanumericString_thenCorrect() {
    int leftLimit = 48; // numeral '0'
    int rightLimit = 122; // letter 'z'
    int targetStringLength = 10;
    Random random = new Random();

    String generatedString = random.ints(leftLimit, rightLimit + 1)
      .filter(i -> (i <= 57 || i >= 65) && (i <= 90 || i >= 97))
      .limit(targetStringLength)
      .collect(StringBuilder::new, StringBuilder::appendCodePoint, StringBuilder::append)
      .toString();

    System.out.println(generatedString);
}
```

범위를 벗어난 문자를 피하기 위해 위의 필터 방법을 사용하여 65에서 90 사이의 유니코드 문자를 생략했다.

## 5. Apache Commons Lang으로 경계 무작위 문자열 생성
Apache의 Commons Lang 라이브러리는 임의의 문자열 생성에 많은 도움이 된다. 문자만 사용하여 제한된 문자열을 생성하는 방법이다.

```java
@Test
public void givenUsingApache_whenGeneratingRandomStringBounded_thenCorrect() {
 
    int length = 10;
    boolean useLetters = true;
    boolean useNumbers = false;
    String generatedString = RandomStringUtils.random(length, useLetters, useNumbers);

    System.out.println(generatedString);
}
```

따라서 Java 예제의 모든 로우 레벨 코드 대신 간단한 한 줄짜리 코드로 수행된다.

## 6. Apache Commons Lang으로 알파벳 문자열 생성
알파벳 문자만 포함하지만 boolean 플래그를 API에 전달하지 않는 제한된 문자열 이다.

```java
@Test
public void givenUsingApache_whenGeneratingRandomAlphabeticString_thenCorrect() {
    String generatedString = RandomStringUtils.randomAlphabetic(10);

    System.out.println(generatedString);
}
```

## 7. Apache Commons Lang으로 영숫자 문자열 생성
동일한 임의의 경계 문자열이 있지만 이번에는 숫자가 있다.

```java
@Test
public void givenUsingApache_whenGeneratingRandomAlphanumericString_thenCorrect() {
    String generatedString = RandomStringUtils.randomAlphanumeric(10);

    System.out.println(generatedString);
}
```

그리고 여기에 일반 Java, Java 8 변형 또는 Apache Commons Library를 사용하여 제한 및 제한되지 않은 문자열을 생성 한다.

> 이 Java 예제에서 java.util.Random을 사용 했지만 주의할 점은 이것이 암호학적으로 안전하지 않다는 것이다. 보안에 민감한 애플리케이션 대신 java.security.SecureRandom 사용을 고려해야 한다.

## [출처 및 참고]
* <https://www.baeldung.com/java-random-string>
