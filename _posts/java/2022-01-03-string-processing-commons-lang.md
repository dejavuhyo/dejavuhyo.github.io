---
title: Apache Commons Lang 3을 사용한 문자열 처리
author: dejavuhyo
date: 2022-01-03 10:20:00 +0900
categories: [Language, Java]
tags: [string-processing-commons-lang, string-processing, commons-lang, apache-commons-lang, 문자열-처리]
---

## 1. Apache Commons Lang 3
Apache Commons Lang 3 라이브러리는 자바의 API의 핵심 클래스의 조작에 대한 지원을 제공한다. 이 지원에는 문자열, 숫자, 날짜, 동시성, 개체 반사 등을 처리하는 메서드가 포함된다.

## 2. Maven Dependency
Commons Lang 3 라이브러리를 사용하려면 다음 종속성을 사용하여 중앙 Maven 저장소에서 가져오기만 하면 된다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```

[여기](https://search.maven.org/classic/#search%7Cga%7C1%7Cg%3A%22org.apache.commons%22%20AND%20a%3A%22commons-lang3%22)에서 라이브러리의 최신 버전을 찾을 수 있다.

## 3. StringUtils
StringUtils 클래스는 문자열에 대한 null-safe 작업을 위한 메서드를 제공한다.

이 클래스의 많은 메서드에는 클래스 [java.lang.String](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/String.html)에 정의된 해당 메서드가 있다. 이것은 null-safe가 아니다. 그러나 String 클래스에 동등한 항목이 없는 여러 메서드에 중점을 둔다.

## 4. containsAny 메소드
containsAny의 메서드는 주어진 문자열이 주어진 문자 집합의 문자를 포함하고 있는지 확인합니다. 이 문자 집합은 String 또는 char varargs 의 형태로 전달될 수 있다.

다음 코드는 결과 확인과 함께 이 메서드의 두 가지 오버로드된 특성을 사용하는 방법을 보여준다.

```java
String string = "baeldung.com";
boolean contained1 = StringUtils.containsAny(string, 'a', 'b', 'c');
boolean contained2 = StringUtils.containsAny(string, 'x', 'y', 'z');
boolean contained3 = StringUtils.containsAny(string, "abc");
boolean contained4 = StringUtils.containsAny(string, "xyz");

assertTrue(contained1);
assertFalse(contained2);
assertTrue(contained3);
assertFalse(contained4);
```

## 5. containsIgnoreCase 메소드
containsIgnoreCase 메서드는 지정된 문자열에 대소문자를 구분하지 않는 방식으로 다른 String이 포함되어 있는지 확인합니다.

다음 코드는 대문자와 소문자가 무시될 때 문자열 "baeldung.com"이 "BAELDUNG"로 구성되는지 확인한다.

```java
String string = "baeldung.com";
boolean contained = StringUtils.containsIgnoreCase(string, "BAELDUNG");

assertTrue(contained);
```

## 6. countMatches 메소드
countMatches 메소드는 주어진 문자열에 문자 또는 하위 문자열이 나타나는 횟수를 계산한다.

다음은 문자열 "welcome to www.baeldung.com" 에서 "w"가 네 번 나타나고 "com"이 두 번 나타나는 것을 확인하는 방법의 데모이다.

```java
String string = "welcome to www.baeldung.com";
int charNum = StringUtils.countMatches(string, 'w');
int stringNum = StringUtils.countMatches(string, "com");

assertEquals(4, charNum);
assertEquals(2, stringNum);
```

## 7. Appending과 Prepending 메소드
appendIfMissing 및 appendIfMissingIgnoreCase 메서드는 각각 대소문자를 구분하지 않고 전달된 접미사로 끝나지 않은 경우 주어진 문자열 끝에 접미사를 추가한다.

마찬가지로 prependIfMissing 및 prependIfMissingIgnoreCase 메서드는 전달된 접두사로 시작하지 않으면 주어진 문자열의 시작 부분에 접두사를 추가한다.

다음 예제에서 appendIfMissing 및 prependIfMissing 메소드는 이러한 접미사를 반복하지 않고 문자열 "baeldung.com"에 접미사와 접두사를 추가하는데 사용된다.

```java
String string = "baeldung.com";
String stringWithSuffix = StringUtils.appendIfMissing(string, ".com");
String stringWithPrefix = StringUtils.prependIfMissing(string, "www.");

assertEquals("baeldung.com", stringWithSuffix);
assertEquals("www.baeldung.com", stringWithPrefix);
```

## 8. 대소문자 변경 방법
String 클래스는 문자열의 모든 String을 대문자 또는 소문자로 변환하는 메서드를 이미 정의한다. 이 하위 섹션은 swapCase, capitalize 및 uncapitalize와 같은 다른 방법으로 문자열의 대소문자를 변경하는 메소드의 사용만 보여준다.

swapCase 메소드는 문자열의 대소문자를 바꾸어 대문자를 소문자로, 소문자를 대문자로 변경한다.

```java
String originalString = "baeldung.COM";
String swappedString = StringUtils.swapCase(originalString);

assertEquals("BAELDUNG.com", swappedString);
```

capitalize 메소드는 주어진 문자열의 첫 번째 문자를 대문자로 변환하고 나머지 모든 문자는 변경하지 않는다.

```java
String originalString = "baeldung";
String capitalizedString = StringUtils.capitalize(originalString);

assertEquals("Baeldung", capitalizedString);
```

uncapitalize 메소드는 주어진 문자열의 첫 번째 문자를 소문자로 변환하고 나머지 모든 문자는 변경하지 않는다.

```java
String originalString = "Baeldung";
String uncapitalizedString = StringUtils.uncapitalize(originalString);

assertEquals("baeldung", uncapitalizedString);
```

## 9. Reversing 메소드
StringUtils 클래스는 문자열을 반전하는 두 가지 메서드를 정의합니다.

reverse 메서드는 문자열의 모든 문자를 반대 순서로 재정렬하는 반면 reverseDelimited 메서드는 지정된 구분 기호로 구분된 문자 그룹의 순서를 변경한다.

다음 코드는 "beldung" 문자열을 반전시키고 결과를 검증한다.

```java
String originalString = "baeldung";
String reversedString = StringUtils.reverse(originalString);

assertEquals("gnudleab", reversedString);
```

reverseDelimited 메소드를 사용하면 문자는 개별적으로가 아니라 그룹으로 반전된다.

```java
String originalString = "www.baeldung.com";
String reversedString = StringUtils.reverseDelimited(originalString, '.');

assertEquals("com.baeldung.www", reversedString);
```

## 10. rotate() 메소드
rotate() 메소드는 순환적으로 문자열의 문자를 여러 위치로 이동한다. 아래의 코드는 문자열 "beldung"의 모든 문자를 오른쪽으로 네 위치 이동하고 결과를 확인한다.

```java
String originalString = "baeldung";
String rotatedString = StringUtils.rotate(originalString, 4);

assertEquals("dungbael", rotatedString);
```

## 11. difference 메소드
difference 메소드는 두 문자열을 비교하여 첫 번째 문자열과 다른 위치부터 시작하여 두 번째 문자열의 나머지를 반환한다.

다음 코드 조각은 두 개의 문자열 "Baeldung Tutorials"와 "Baeldung Courses"를 비교하여 결과를 확인한다.

```java
String tutorials = "Baeldung Tutorials";
String courses = "Baeldung Courses";
String diff1 = StringUtils.difference(tutorials, courses);
String diff2 = StringUtils.difference(courses, tutorials);

assertEquals("Courses", diff1);
assertEquals("Tutorials", diff2);
```

## [출처 및 참고]
* [https://www.baeldung.com/string-processing-commons-lang](https://www.baeldung.com/string-processing-commons-lang)
