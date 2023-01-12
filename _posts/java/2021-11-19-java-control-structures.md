---
title: Java 제어 구조
author: dejavuhyo
date: 2021-11-19 09:30:00 +0900
categories: [Language, Java]
tags: [java-control-structures, control-structures, 자바-제어-구조, 제어-구조]
---

## 1. 제어 구조
가장 기본적인 의미에서 프로그램은 명령어 목록이다. 제어 구조는 해당 명령을 통해 취하는 경로를 변경할 수 있는 프로그래밍 블록이다.

세 가지 종류의 제어 구조가 있다.

* 두 개 이상의 경로 중에서 선택하는 데 사용하는 조건부 분기이다. Java에는 if/else/else if, 삼항 연산자 및 switch 세 가지 유형이 있다.

* 여러 값/개체를 반복하고 특정 코드 블록을 반복적으로 실행하는 데 사용되는 루프이다. Java의 기본 루프 유형은 while, while이다.

* 분기 문, 루프에서 제어 흐름을 변경하는 데 사용됩니다. Java에는 break와 continue의 두 가지 유형이 있다.

## 2. If, Else, Else If
if/else 문은 제어 구조의 가장 기본적인 뿐만 아니라 프로그래밍 의사 결정의 가장 기초가 고려될 수 있다.

if는 단독으로 사용할 수 있지만 가장 일반적인 사용 시나리오는 if/else를 사용하여 두 경로 중에서 선택하는 것이다.

```java
if (count > 2) {
    System.out.println("Count is higher than 2");
} else {
    System.out.println("Count is lower or equal than 2");
}
```

이론적으로 if/else 블록을 무한히 연결하거나 중첩할 수 있지만 이는 코드 가독성을 떨어뜨리므로 권장하지 않는다.

## 3. 삼항 연산자
삼항 연산자를 if/else문 처럼 작동하는 약식 표현으로 사용할 수 있다.

```java
if (count > 2) {
    System.out.println("Count is higher than 2");
} else {
    System.out.println("Count is lower or equal than 2");
}
```

이것을 다음과 같이 삼항으로 리팩토링할 수 있다.

```java
System.out.println(count > 2 ? "Count is higher than 2" : "Count is lower or equal than 2");
```

## 4. Switch
선택할 수 있는 경우가 여러 개인 경우 switch문을 사용할 수 있다.

```java
int count = 3;
switch (count) {
case 0:
    System.out.println("Count is equal to 0");
    break;
case 1:
    System.out.println("Count is equal to 1");
    break;
default:
    System.out.println("Count is either negative, or higher than 1");
    break;
}
```

세 개 이상의 if/else문은 읽기 어려울 수 있다. 가능한 해결 방법 중 하나로 위와 같이 switch를 사용할 수 있다.

또한 switch에는 사용하기 전에 기억해야 하는 범위와 입력 제한이 있다는 점을 염두해야 한다.

## 5. Loops
동일한 코드를 연속해서 여러 번 반복해야 할 때 루프를 사용한다.

```java
for (int i = 1; i <= 50; i++) {
    methodToRepeat();
}

int whileCounter = 1;
while (whileCounter <= 50) {
    methodToRepeat();
    whileCounter++;
}
```

위의 두 코드 블록 모두 methodToRepeat를 50번 호출한다.

## 6. Break
루프에서 일찍 종료 하려면 break를 사용해야 한다.

```java
List<String> names = getNameList();
String name = "John Doe";
int index = 0;
for ( ; index < names.length; index++) {
    if (names[index].equals(name)) {
        break;
    }
}
```

여기에서 이름 목록에서 이름을 찾고 있으며 일단 찾은 후에는 검색을 중단하고 싶다.

루프는 일반적으로 완료되지만 여기에서 break를 사용하여 이를 단락시키고 일찍 종료한다.

## 7. Continue
간단히 말해서,  continue는 루프의 나머지 부분을 건너뛰는 것을 의미한다.

```java
List<String> names = getNameList();
String name = "John Doe";
String list = "";
for (int i = 0; i < names.length; i++) { 
    if (names[i].equals(name)) {
        continue;
    }
    list += names[i];
}
```

여기서는 중복된 이름을 목록에 추가하는 것을 건너뛴다.

여기에서 보았듯이 break 및 continue는 반복할 때 편리할 수 있지만 return 문이나 다른 논리로 종종 다시 작성할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-control-structures](https://www.baeldung.com/java-control-structures)
