---
title: Java 문자열 비교
author: dejavuhyo
date: 2022-01-07 09:45:00 +0900
categories: [Language, Java]
tags: [java-compare-strings, compare-strings, java-strings, 자바-문자열-비교, 문자열-비교, 문자열, 자바-문자열]
---

## 1. 문자열 비교와 문자열 클래스

### 1) "==" 비교 연산자 사용
텍스트 값을 비교하기 위해 "==" 연산자를 사용하는 것은 Java 초보자가 저지르는 가장 흔한 실수 중 하나이다. 이는 "=="가 두 가지의 문자열의 참조 동일성을 검사 문자열, 같은 객체를 참조하거나 하지 않을 경우만 확인하기 때문에 올바르지 않다.

이 동작의 예이다.

```java
String string1 = "using comparison operator";
String string2 = "using comparison operator";
String string3 = new String("using comparison operator");

assertThat(string1 == string2).isTrue();
assertThat(string1 == string3).isFalse();
```

위의 예에서 두 변수가 동일한 문자열 리터럴을 가리키기 때문에 첫 번째 어설션은 true 이다.

반면에 string1은 리터럴로 생성되고 string3 은 new 연산자를 사용하여 생성되기 때문에 두 번째 어설션은 false이다. 따라서 서로 다른 객체를 참조한다.

### 2) equals() 사용
문자열 클래스는 개체에서 상속된 equals()를 재정의 한다. 이 메서드는 주소를 무시하고 두 개의 문자열을 문자별로 비교 한다.

길이가 같고 문자의 순서가 같으면 동일한 것으로 간주한다.

```java
String string1 = "using equals method";
String string2 = "using equals method";

String string3 = "using EQUALS method";
String string4 = new String("using equals method");

assertThat(string1.equals(string2)).isTrue();
assertThat(string1.equals(string4)).isTrue();

assertThat(string1.equals(null)).isFalse();
assertThat(string1.equals(string3)).isFalse();
```

이 예제에서 string1, string2 및 string4 변수는 주소에 관계없이 대소문자와 값이 같으므로 동일하다.

string3의 경우 메소드는 대소문자를 구분하므로 false를 반환한다.

또한 두 문자열 중 하나라도 null이면 메소드는 false를 반환한다.

### 3) equalsIgnoreCase() 사용
equalsIgnoreCase() 메소드는 boolean 값을 반환한다. 이름에서 알 수 있듯이 이 메서드는 Strings를 비교하는 동안 문자의 대소문자를 무시한다.

```java
String string1 = "using equals ignore case";
String string2 = "USING EQUALS IGNORE CASE";

assertThat(string1.equalsIgnoreCase(string2)).isTrue();
```

### 4) compareTo() 사용
compareTo() 메소드는 int 유형 값을 반환하고 사전 또는 자연 순서에 따라 문자 사전순으로 두 문자열 문자를 비교한다.

이 메서드는 두 문자열이 같거나 둘 다 null이면 0을 반환하고 첫 번째 문자열이 인수 앞에 오는 경우 음수를 반환하며 첫 번째 문자열이 문자열 뒤에 오는 경우 0보다 큰 숫자를 반환한다.

예이다.

```java
String author = "author";
String book = "book";
String duplicateBook = "book";

assertThat(author.compareTo(book))
  .isEqualTo(-1);
assertThat(book.compareTo(author))
  .isEqualTo(1);
assertThat(duplicateBook.compareTo(book))
  .isEqualTo(0);
```

### 5) compareToIgnoreCase() 사용
compareToIgnoreCase()는 대소문자를 무시한다는 점을 제외하면 이전 메서드와 유사하다.

```java
String author = "Author";
String book = "book";
String duplicateBook = "BOOK";

assertThat(author.compareToIgnoreCase(book))
  .isEqualTo(-1);
assertThat(book.compareToIgnoreCase(author))
  .isEqualTo(1);
assertThat(duplicateBook.compareToIgnoreCase(book))
  .isEqualTo(0);
```

## 2. 객체 클래스와 문자열 비교
Objects는 정적 equals() 메서드가 포함된 유틸리티 클래스로, 두 개의 문자열을 비교하는데 유용하다.

이 메서드는 "=="와 같은 주소를 사용하여 먼저 두 문자열을 비교하여 두 문자열 이 같으면 true를 반환 한다. 결과적으로 두 인수가 모두 null 이면 true를 반환 하고 정확히 하나의 인수가 null이면 false를 반환한다.

그렇지 않으면 전달된 인수 유형 클래스의 equals() 메소드를 호출한다. 이 경우는 String의 클래스 equals() 메소드이다. 이 메서드는 내부적으로 String 클래스의 equals() 메서드를 호출하기 때문에 대소문자를 구분한다.

이것의 테스트이다.

```java
String string1 = "using objects equals";
String string2 = "using objects equals";
String string3 = new String("using objects equals");

assertThat(Objects.equals(string1, string2)).isTrue();
assertThat(Objects.equals(string1, string3)).isTrue();

assertThat(Objects.equals(null, null)).isTrue();
assertThat(Objects.equals(null, string1)).isFalse();
```

## 3. Apache Commons 문자열 비교
Apache Commons 라이브러리에는 String 관련 작업을 위한 StringUtils라는 유틸리티 클래스가 포함되어 있다. 이것은 또한 문자열 비교를 위한 몇 가지 매우 유익한 방법을 가지고 있다.

### 1) equals()와 equalsIgnoreCase() 사용
StringUtils 클래스의 equals() 메서드는 null 값도 처리 하는 String 클래스 메서드 equals()의 향상된 버전이다.

```java
assertThat(StringUtils.equals(null, null))
  .isTrue();
assertThat(StringUtils.equals(null, "equals method"))
  .isFalse();
assertThat(StringUtils.equals("equals method", "equals method"))
  .isTrue();
assertThat(StringUtils.equals("equals method", "EQUALS METHOD"))
  .isFalse();
```

StringUtils의 equalsIgnoreCase() 메서드는 boolean 값을 반환 한다. 이것은 Strings에서 문자의 대소문자를 무시한다는 점을 제외하고는 equals()와 유사하게 작동 한다.

```java
assertThat(StringUtils.equalsIgnoreCase("equals method", "equals method"))
  .isTrue();
assertThat(StringUtils.equalsIgnoreCase("equals method", "EQUALS METHOD"))
  .isTrue();
```

### 2) equalsAny()와 equalsAnyIgnoreCase() 사용
equalsAny() 메소드의 첫 번째 인자는 문자열이고 두 번째는 multi-args 타입의 CharSequence이다. 메소드는 지정된 다른 문자열이 첫 번째 문자열 대소문자와 민감하게 일치할 경우 true를 반환한다.

그렇지 않으면 false가 반환된다.

```java
assertThat(StringUtils.equalsAny(null, null, null))
  .isTrue();
assertThat(StringUtils.equalsAny("equals any", "equals any", "any"))
  .isTrue();
assertThat(StringUtils.equalsAny("equals any", null, "equals any"))
  .isTrue();
assertThat(StringUtils.equalsAny(null, "equals", "any"))
  .isFalse();
assertThat(StringUtils.equalsAny("equals any", "EQUALS ANY", "ANY"))
  .isFalse();
```

equalsAnyIgnoreCase() 메소드는 equalsAny() 메소드와 유사하게 작동하지만 대소문자 표기도 무시한다.

```java
assertThat(StringUtils.equalsAnyIgnoreCase("ignore case", "IGNORE CASE", "any")).isTrue();
```

### 4) compare()과 compareIgnoreCase() 사용
StringUtils 클래스의 compare() 메서드는 String 클래스의 compareTo() 메서드의 null 안전 버전이며 null 이 아닌 값보다 작은 null 값을 고려하여 null 값을 처리 한다. 두 개의 null 값은 동일한 것으로 간주된다.

또한 이 메소드를 사용하여 null 항목이 있는 문자열 목록을 정렬할 수 있다.

```java
assertThat(StringUtils.compare(null, null))
  .isEqualTo(0);
assertThat(StringUtils.compare(null, "abc"))
  .isEqualTo(-1);
assertThat(StringUtils.compare("abc", "bbc"))
  .isEqualTo(-1);
assertThat(StringUtils.compare("bbc", "abc"))
  .isEqualTo(1);
```

compareIgnoreCase() 메소드는 대소문자를 무시한다는 점을 제외하고는 비슷하게 동작한다.

```java
assertThat(StringUtils.compareIgnoreCase("Abc", "bbc"))
  .isEqualTo(-1);
assertThat(StringUtils.compareIgnoreCase("bbc", "ABC"))
  .isEqualTo(1);
assertThat(StringUtils.compareIgnoreCase("abc", "ABC"))
  .isEqualTo(0);
```

두 메서드는 nullIsLess 옵션과 함께 사용할 수도 있다. 이것은 null 값이 더 작은 것으로 간주되어야 하는지 여부를 결정 하는 세 번째 boolean 인수이다.

nullIsLess가 true이면 null 값이 다른 문자열보다 낮고 nullIsLess가 false이면 더 높다.

예이다.

```java
assertThat(StringUtils.compare(null, "abc", true))
  .isEqualTo(-1);
assertThat(StringUtils.compare(null, "abc", false))
  .isEqualTo(1);
```

세 번째 부울 인수가 있는 compareIgnoreCase() 메서드는 대소문자를 무시하는 경우를 제외하고 비슷하게 작동한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-compare-strings](https://www.baeldung.com/java-compare-strings)
