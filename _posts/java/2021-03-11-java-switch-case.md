---
title: Java switch/case 문
author: dejavuhyo
date: 2021-03-10 06:00:00 +0900
categories: [Language, Java]
tags: [java-switch-case, switch-case]
---

## 1. switch/case 문 이란
switch/case 문은 if 문과 비슷하지만 좀 더 정형화된 모습의 제어문이다.

* switch/case 문의 구조

```text
switch(입력변수) {
    case 입력값1: ...
         break;
    case 입력값2: ...
         break;
    ...
    default: ...
         break;
}
```

입력변수의 값과 일치하는 case 입력값(입력값1, 입력값2, ...)이 있다면 해당 case 문 하위의 문장이 실행된다.

case 문마다 break 라는 문장이 있는데 해당 case 문을 실행한 뒤 switch 문을 빠져나가기 위한 것이다. 만약 break 문이 빠져 있다면 그다음의 case 문이 실행되게 된다.

입력값이 정형화되어 있는 경우 if 문보다는 switch/case 문을 쓰는 것이 가독성에서 좀 더 유리하다. switch/case 문은 if else 구조로 변경이 가능하지만 if else 구조로 작성된 모든 코드를 switch 문으로 변경할 수는 없다.

## 2. 성능
if/else 문과 switch/case 문은 성능과 메모리 사용의 문제이다. 성능이 중요한 경우에는 switch/case 문을, 메모리 사용이 중요한 경우에는 if/else 문을 사용하면 좋다.

switch/case 문에서 case의 값은 순차적으로 감소 혹은 증가하는 것이 메모리 관리면에서 좋다.

## 3. 사용

### 1) int switch/case 예

```java
public class IntSwitchCaseExample {
    public static void main(String[] args) {
        int month = 8;
        String monthString = "";
        switch (month) {
            case 1:
                monthString = "January";
                break;
            case 2:
                monthString = "February";
                break;
            case 3:
                monthString = "March";
                break;
            case 4:
                monthString = "April";
                break;
            case 5:
                monthString = "May";
                break;
            case 6:
                monthString = "June";
                break;
            case 7:
                monthString = "July";
                break;
            case 8:
                monthString = "August";
                break;
            case 9:
                monthString = "September";
                break;
            case 10:
                monthString = "October";
                break;
            case 11:
                monthString = "November";
                break;
            case 12:
                monthString = "December";
                break;
            default:
                monthString = "Invalid month";
                break;
        }
        System.out.println(monthString);
    }
}
```

### 2) string switch/case 예

```java
public class StringSwitchCaseExample {
    public static void main(String[] args) {
        String val = "i";
        String monthString = "";
        switch (val) {
            case "a":
                monthString = "January";
                break;
            case "b":
                monthString = "February";
                break;
            case "c":
                monthString = "March";
                break;
            case "d":
                monthString = "April";
                break;
            case "e":
                monthString = "May";
                break;
            case "f":
                monthString = "June";
                break;
            case "g":
                monthString = "July";
                break;
            case "h":
                monthString = "August";
                break;
            case "i":
                monthString = "September";
                break;
            case "j":
                monthString = "October";
                break;
            case "k":
                monthString = "November";
                break;
            case "l":
                monthString = "December";
                break;
            default:
                monthString = "Invalid month";
                break;
        }
        System.out.println(monthString);
    }
}
```

## 출처 및 참고
* <https://wikidocs.net/263>
* <http://blog.naver.com/PostView.nhn?blogId=kki2406&logNo=80041410085>
