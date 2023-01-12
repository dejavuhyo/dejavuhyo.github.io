---
title: 문자열에서 Char 발생 횟수 계산
author: dejavuhyo
date: 2021-12-24 09:30:00 +0900
categories: [Language, Java]
tags: [java-count-chars, count-chars, java-char, 문자열-char-횟수, char-횟수]
---

## 1. String에서 char의 발생 횟수 계산
Java의 String에서 char의 발생 횟수를 계산하는 방법에는 여러 가지가 있다.

먼저 핵심 Java 라이브러리를 사용한 다음 Spring 및 Guava와 같은 다른 라이브러리 및 프레임워크를 사용하여 문자 수를 계산하는 방법에 대한 몇 가지 예에 중점을 둔다.

## 2. 코어 자바 라이브러리 사용

### 1) 명령적 접근
일부 개발자는 코어 Java를 사용하는 것을 선호할 수 있다. 문자열에서 char의 발생 횟수를 계산하는 방법에는 여러 가지가 있다.

```java
String someString = "elephant";
char someChar = 'e';
int count = 0;
 
for (int i = 0; i < someString.length(); i++) {
    if (someString.charAt(i) == someChar) {
        count++;
    }
}
assertEquals(2, count);
```

당연히 이것이 작동하지만 더 나은 방법이 있다.

### 2) 재귀 사용
덜 분명하지만 흥미로운 솔루션은 재귀를 사용하는 것이다.

```java
private static int countOccurences(
  String someString, char searchedChar, int index) {
    if (index >= someString.length()) {
        return 0;
    }
    
    int count = someString.charAt(index) == searchedChar ? 1 : 0;
    return count + countOccurences(
      someString, searchedChar, index + 1);
}
```

다음과 같은 방법으로 이 재귀적 메서드를 호출할 수 있다. `useRecursionToCountChars("elephant", 'e', 0)`

### 3) 정규식 사용
또 다른 방법은 정규식을 사용하는 것이다.

```java
Pattern pattern = Pattern.compile("[^e]*e");
Matcher matcher = pattern.matcher("elephant");
int count = 0;
while (matcher.find()) {
    count++;
}
 
assertEquals(2, count);
```

이 솔루션은 기술적으로 정확하지만 차선책이다. 문자열에서 문자의 발생 횟수를 찾는 것과 같은 간단한 문제를 해결하기 위해 매우 강력한 정규식을 사용하는 것은 과도하기 때문이다.

### 4) Java 8 기능 사용
Java 8에서 사용할 수 있는 새로운 기능은 여기에서 매우 유용할 수 있다.

스트림과 람다를 사용하여 개수를 구현한다.

```java
String someString = "elephant";
long count = someString.chars().filter(ch -> ch == 'e').count();
assertEquals(2, count);

long count2 = someString.codePoints().filter(ch -> ch == 'e').count();
assertEquals(2, count2);
```

따라서 이것은 핵심 라이브러리를 사용하는 명확하고 읽기 쉬운 솔루션이다.

## 3. 외부 라이브러리 사용
외부 라이브러리의 유틸리티를 사용하는 몇 가지 솔루션이다.

### 1) StringUtils 사용
일반적으로 자체 솔루션을 발명하는 대신 기존 솔루션을 사용하는 것이 항상 더 좋다. commons.lang.StringUtils 클래스는 주어진 문자열의 chars 또는 하위 스트링을 계산하는데 사용할 수 있는 countMatches() 메소드를 제공한다.

먼저 적절한 종속성을 포함해야 한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```

[Maven Central](https://search.maven.org/classic/#search%7Cga%7C1%7Cg%3A%22org.apache.commons%22%20AND%20a%3A%22commons-lang3%22)에서 최신 버전을 찾을 수 있다.

이제 countMatches()를 사용 하여 "elephant" 문자열 리터럴의 e문자 수를 계산한다.

```java
int count = StringUtils.countMatches("elephant", "e");
assertEquals(2, count);
```

### 2) Guava 사용
구아바 또한 문자를 계산하는 데 도움이 될 수 있다. 종속성을 정의해야 한다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>21.0</version>
</dependency>
```

[Maven Central](https://search.maven.org/classic/#search%7Cga%7C1%7Cg%3A%22com.google.guava%22%20AND%20a%3A%22guava%22)에서 최신 버전을 찾을 수 있다.

Guava가 문자 수를 세는 데 어떻게 빠르게 도움이 되는지 본다.

```java
int count = CharMatcher.is('e').countIn("elephant");
assertEquals(2, count);
```

### 3) 스프링 사용
당연히, 단순히 문자를 계산하기 위해 Spring Framework를 프로젝트에 추가하는 것은 의미가 없다.

그러나 프로젝트에 이미 있는 경우 countOccurenceOf() 메서 를 사용하기만 하면 된다.

```java
int count = StringUtils.countOccurrencesOf("elephant", "e");
assertEquals(2, count);
```

> StringUtils, Guava 또는 Spring의 기존 유틸리티를 사용하는 것이 좋다. 그러나 일반 Java만 사용하는 것이 선호되는 경우 Java 8로 이를 수행할 수 있는 몇 가지 가능성을 제공한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-count-chars](https://www.baeldung.com/java-count-chars)
