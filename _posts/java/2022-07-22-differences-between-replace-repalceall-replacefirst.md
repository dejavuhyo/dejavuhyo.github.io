---
title: Java replace(), repalceAll(), replaceFirst() 차이점
author: dejavuhyo
date: 2022-07-22 10:40:00 +0900
categories: [Language, Java]
tags: [java-replace-replaceall-replacefirst, java-replace, java-replaceall, java-replacefirst, replace, replaceall, replacefirst, 자바-replace-replaceall, 자바-replace, 자바-replaceall, 자바-replacefirst]
---

## 1. repalce()
String replace(CharSequence target, CharSequence replacement)로, 첫번째 매개변수는 변환하고자 하는 대상이 될 문자열, 두번째 매개변수는 변환할 문자 값이다.

```java
public class ReplaceTest {
    public static void main(String[] args) {
        String replaceTest = "리플레이스 테스트";
        System.out.println(replaceTest.replace("리플레이스", "replace"));
    }
}
```

## 2. repalceAll()
String replaceAll(String regex, String replacement)로, replaceAll() 함수는 대상 문자열을 원하는 문자값으로 변환하는 함수이다.

첫번째 매개변수는 변환하고자 하는 대상이 될 문자열이고 두번째 매개변수는 변환할 문자 값이다.

```java
public class ReplaceAllTest {
    public static void main(String[] args) {
        String replaceAllTest = "리플레이스의 리플레이스 테스트";
        System.out.println(replaceAllTest.replaceAll("리플레이스", "replaceAll"));
    }
}
```

## 3. replaceFirst()
String replaceFirst(String regex, String replacement)로 처음으로 만나는 패턴만 대치하고, 정규식을 활용할 수 있다.

```java
public class ReplaceFirstTest {
    public static void main(String[] args) {
        String replaceFirstTest = "past is just past";
        System.out.println(replaceFirstTest.replaceFirst("past", "future")); // future is just past
        System.out.println(replaceFirstTest.replaceFirst("[a-z]", "1")); // 1ast is just past
        System.out.println(replaceFirstTest.replaceFirst("t$", "1")); // past is just pas1
        System.out.println(replaceFirstTest.replaceFirst(".", "?")); // ?ast is just past
    }
}
```

## 4. repalce() vs repalceAll()
replace()와 replaceAll()의 차이점은 인자 값의 형태에서 CharSequence와 String 이라는 차이점이다.

또한,  replaceAll()의 regex는 '정규 표현식'을 의미한다. 즉, replaceAll()은 정규표현식 사용이 가능하다.

```java
public class AllTest {
    public static void main(String[] args) {
        String allTest = "aaabbbvccacfgdracabtghd";
        System.out.println(allTest.replace("ab", "0")); // aa0bbvccacfgdrac0tghd
        System.out.println(allTest.replaceAll("[ab]", "0")); // 000000vcc0cfgdr0c00tghd
    }
}
```

## [출처 및 참고]
* <https://mine-it-record.tistory.com/127>
* <https://veneas.tistory.com/entry/Java-%EB%AC%B8%EC%9E%90%EC%97%B4-%EB%8C%80%EC%B9%98%ED%95%98%EA%B8%B0replace-replaceAll-replaceFirst>
