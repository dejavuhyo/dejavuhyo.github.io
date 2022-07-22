---
title: Java replace()와 repalceAll() 차이점
author: dejavuhyo
date: 2022-07-20 10:40:00 +0900
categories: [Language, Java]
tags: [java-replace-replaceall, java-replace, java-replaceall, replace, replaceall, 자바-replace-replaceall, 자바-replace, 자바-replaceall]
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

## 3. repalce() vs repalceAll()
replace()와 replaceAll()의 차이점은 인자 값의 형태에서 CharSequence와 String 이라는 차이점이다.

또한,  replaceAll()의 regex는 '정규 표현식'을 의미한다. 즉, replaceAll()은 정규표현식 사용이 가능하다.

```java
public class AllTest {
    public static void main(String[] args) {
        String allTest = "aaabbbvccacfgdracabtghd";

        System.out.println(allTest.replace("ab", "0"));
        // aa0bbvccacfgdrac0tghd

        System.out.println(allTest.replaceAll("[ab]", "0"));
        // 000000vcc0cfgdr0c00tghd
    }
}
```

## [출처 및 참고]
* <https://mine-it-record.tistory.com/127>
