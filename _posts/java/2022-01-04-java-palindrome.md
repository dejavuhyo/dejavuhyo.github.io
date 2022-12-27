---
title: Java 문자열 Palindrome 확인
author: dejavuhyo
date: 2022-01-04 09:30:00 +0900
categories: [Language, Java]
tags: [java-palindrome, palindrome, 자바-palindrome, 자바-문자열]
---

## 1. Palindrome
Palindrome은 단어, 구, 숫자 또는 "madam" 또는 "racecar"와 같이 앞뒤로 똑같이 읽는 일련의 문자이다.

## 2. Palindrome 확인 방법

### 1) 간단한 접근
주어진 문자열을 한 번에 한 문자씩 앞뒤로 동시에 반복할 수 있다. 일치하는 항목이 있으면 루프가 계속된다. 그렇지 않으면 루프가 종료된다.

```java
public boolean isPalindrome(String text) {
    String clean = text.replaceAll("\\s+", "").toLowerCase();
    int length = clean.length();
    int forward = 0;
    int backward = length - 1;
    while (backward > forward) {
        char forwardChar = clean.charAt(forward++);
        char backwardChar = clean.charAt(backward--);
        if (forwardChar != backwardChar)
            return false;
    }
    return true;
}
```

### 2) 문자열 반전
Palindrome을 확인할 때 StringBuilder 및 StringBuffer 클래스의 API 메소드를 사용하거나 이러한 클래스 없이 String을 되돌릴 수 있다.

먼저 helper API가 없는 코드 구현이다.

```java
public boolean isPalindromeReverseTheString(String text) {
    StringBuilder reverse = new StringBuilder();
    String clean = text.replaceAll("\\s+", "").toLowerCase();
    char[] plain = clean.toCharArray();
    for (int i = plain.length - 1; i >= 0; i--) {
        reverse.append(plain[i]);
    }
    return (reverse.toString()).equals(clean);
}
```

위의 스니펫에서 단순히 마지막 문자에서 주어진 문자열을 반복하고 각 문자를 다음 문자에 추가한다.

마지막으로 주어진 문자열과 반전된 문자열 사이의 동등성을 테스트한다.

API 메서드를 사용하여 동일한 동작을 달성할 수 있다.

간단한 데모이다.

```java
public boolean isPalindromeUsingStringBuilder(String text) {
    String clean = text.replaceAll("\\s+", "").toLowerCase();
    StringBuilder plain = new StringBuilder(clean);
    StringBuilder reverse = plain.reverse();
    return (reverse.toString()).equals(clean);
}

public boolean isPalindromeUsingStringBuffer(String text) {
    String clean = text.replaceAll("\\s+", "").toLowerCase();
    StringBuffer plain = new StringBuffer(clean);
    StringBuffer reverse = plain.reverse();
    return (reverse.toString()).equals(clean);
}
```

코드 스니펫에서 StringBuilder 및 StringBuffer API의 reverse() 메서드를 호출 하여 주어진 문자열을 역전시키고 동일성을 테스트한다.

### 3) 스트림 API 사용
IntStream을 사용하여 솔루션을 제공할 수 있다.

```java
public boolean isPalindromeUsingIntStream(String text) {
    String temp  = text.replaceAll("\\s+", "").toLowerCase();
    return IntStream.range(0, temp.length() / 2)
      .noneMatch(i -> temp.charAt(i) != temp.charAt(temp.length() - i - 1));
}
```

위의 스니펫에서 문자열의 각 끝에서 나온 문자 쌍 중 어느 것도 술어 조건을 충족하지 않는지 확인한다.

### 4) 재귀 사용
재귀는 이러한 종류의 문제를 해결하는 매우 인기 있는 방법이다. 예제에서 주어진 String을 재귀적으로 반복하고 그것이 회문인지 아닌지를 알아보기 위해 테스트한다.

```java
public boolean isPalindromeRecursive(String text){
    String clean = text.replaceAll("\\s+", "").toLowerCase();
    return recursivePalindrome(clean,0,clean.length()-1);
}

private boolean recursivePalindrome(String text, int forward, int backward) {
    if (forward == backward) {
        return true;
    }
    if ((text.charAt(forward)) != (text.charAt(backward))) {
        return false;
    }
    if (forward < backward + 1) {
        return recursivePalindrome(text, forward + 1, backward - 1);
    }

    return true;
}
```

## [출처 및 참고]
* <https://www.baeldung.com/java-palindrome>
