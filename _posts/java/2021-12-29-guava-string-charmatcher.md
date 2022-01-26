---
title: 구아바 CharMatcher
author: dejavuhyo
date: 2021-12-29 09:55:00 +0900
categories: [Language, Java]
tags: [guava-string-charmatcher, guava-string, guava-charmatcher, guava, 구아바-charmatcher, 구아바-string-charmatcher, 구아바-charmatcher, 구아바]
---

## 1. 문자열에서 특수 문자 제거
String에서 모든 특수 문자를 제거한다.

다음 예제에서 keepFrom()을 사용하여 숫자나 문자가 아닌 모든 문자를 제거한다.

```java
@Test
public void whenRemoveSpecialCharacters_thenRemoved(){
    String input = "H*el.lo,}12";
    CharMatcher matcher = CharMatcher.javaLetterOrDigit();
    String result = matcher.retainFrom(input);

    assertEquals("Hello12", result);
}
```

## 2. 문자열에서 비 ASCII 문자 제거
다음 예제와 같이 CharMatcher를 사용하여 문자열에서 ASCII가 아닌 문자를 제거 할 수도 있다.

```java
@Test
public void whenRemoveNonASCIIChars_thenRemoved() {
    String input = "あhello₤";

    String result = CharMatcher.ascii().retainFrom(input);
    assertEquals("hello", result);

    result = CharMatcher.inRange('0', 'z').retainFrom(input);
    assertEquals("hello", result);
}
```

## 3. 문자 집합에 없는 문자 제거
주어진 Charset에 속하지 않는 문자를 제거하는 방법이다. 다음 예에서는 "cp437" Charset에 속하지 않는 문자를 제거한다.

```java
@Test
public void whenRemoveCharsNotInCharset_thenRemoved() {
    Charset charset = Charset.forName("cp437");
    CharsetEncoder encoder = charset.newEncoder();

    Predicate<Character> inRange = new Predicate<Character>() {
        @Override
        public boolean apply(Character c) {
            return encoder.canEncode(c);
        }
    };

    String result = CharMatcher.forPredicate(inRange)
                               .retainFrom("helloは");
    assertEquals("hello", result);
}
```

* 참고: CharsetEncoder를 사용 하여 주어진 Character가 주어진 Charset으로 인코딩될 수 있는지 확인 하는 Predicate를 생성했다.

## 4. 문자열 유효성 검사
CharMatcher를 사용하여 문자열을 검증하는 방법이다.

모든 문자가 조건과 일치하는지 확인하기 위해 matchAllOf()를 사용할 수 있다. 그리고 문자열 문자에 조건이 적용되지 않는지 확인하기 위해 matchNoneOf()를 사용할 수 있다.

다음 예에서는 String이 소문자인지, 'e' 문자가 하나 이상 포함되어 있고 숫자가 포함되어 있지 않은지 확인한다.

```java
@Test
public void whenValidateString_thenValid(){
    String input = "hello";

    boolean result = CharMatcher.javaLowerCase().matchesAllOf(input);
    assertTrue(result);

    result = CharMatcher.is('e').matchesAnyOf(input);
    assertTrue(result);

    result = CharMatcher.javaDigit().matchesNoneOf(input);
    assertTrue(result);
}
```

## 5. 문자열 자르기
CharMatcher를 사용하여 String을 trim하는 방법이다.

다음 예제에서는 trimLeading(), trimTrailing 및 trimFrom()을 사용하여 String을 trim한다.

```java
@Test
public void whenTrimString_thenTrimmed() {
    String input = "---hello,,,";

    String result = CharMatcher.is('-').trimLeadingFrom(input);
    assertEquals("hello,,,", result);

    result = CharMatcher.is(',').trimTrailingFrom(input);
    assertEquals("---hello", result);

    result = CharMatcher.anyOf("-,").trimFrom(input);
    assertEquals("hello", result);
}
```

## 6. 문자열 축소
CharMatcher를 사용하여 String을 축소하는 방법이다.

다음 예에서 접힌 공백을 '–'로 바꾸기 위해 collapseFrom()를 사용한다.

```java
@Test
public void whenCollapseFromString_thenCollapsed() {
    String input = "       hel    lo      ";

    String result = CharMatcher.is(' ').collapseFrom(input, '-');
    assertEquals("-hel-lo-", result);

    result = CharMatcher.is(' ').trimAndCollapseFrom(input, '-');
    assertEquals("hel-lo", result);
}
```

## 7. 문자열에서 바꾸기
다음 예제와 같이 CharMatcher를 사용하여 문자열의 특정 문자를 바꿀 수 있다.

```java
@Test
public void whenReplaceFromString_thenReplaced() {
    String input = "apple-banana.";

    String result = CharMatcher.anyOf("-.").replaceFrom(input, '!');
    assertEquals("apple!banana!", result);

    result = CharMatcher.is('-').replaceFrom(input, " and ");
    assertEquals("apple and banana.", result);
}
```

## 8. 문자 발생 횟수 계산
CharMatcher를 사용하여 문자 발생 횟수를 계산하는 방법이다.

다음 예에서는 `'a':'h'` 사이의 쉼표와 문자를 계산한다.

```java
@Test
public void whenCountCharInString_thenCorrect() {
    String input = "a, c, z, 1, 2";

    int result = CharMatcher.is(',').countIn(input);
    assertEquals(4, result);

    result = CharMatcher.inRange('a', 'h').countIn(input);
    assertEquals(2, result);
}
```

## [출처 및 참고]
* <https://www.baeldung.com/guava-string-charmatcher>
