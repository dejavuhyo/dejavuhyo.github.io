---
title: Java 문자 비교
author: dejavuhyo
date: 2022-10-25 09:10:00 +0900
categories: [Language, Java]
tags: [java-compare-characters, compare-characters, java-characters, characters, java-compare, java-compareto, java-equals, character-compare, character-compareto, object-equals, objects-equals, 자바-문자-비교, 문자-비교, 자바-문자, 문자]
---

## 1. 원시 문자 비교

### 1) 관계 연산자 사용
일반적으로 문자를 비교하는 가장 간단한 방법은 관계 연산자를 사용하는 것이다.

요컨대, 문자는 ASCII 코드의 순서에 따라 Java에서 비교된다.

```java
assertFalse('a' == 'A');
assertTrue('a' < 'v');
assertTrue('F' > 'D');
```

### 2) Character.compare()  메서드 사용
마찬가지로 다른 방법은 Character 클래스의 `compare()` 메서드를 사용하는 것이다.

간단히 말해서 Character 클래스는 객체의 기본 유형 char 값을 래핑 한다. `compare()` 메서드는 두 개의 char 매개 변수를 받아 수치상으로 비교한다.

```java
assertTrue(Character.compare('C', 'C') == 0);
assertTrue(Character.compare('f', 'A') > 0);
assertTrue(Character.compare('Y', 'z') < 0);
```

위와 같이 compare(char, char b) 메서드는 int 값을 반환한다. a와 b의 ASCII 코드의 차이를 나타낸다.

반환된 값은 두 char 값이 동일하면 0이고, a < b이면 0보다 작고, 그렇지 않으면 0보다 크다.

## 2. 캐릭터 객체 비교

### 1) Character.compareTo() 메서드 사용
Character 클래스는 두 문자 객체를 수치상으로 비교하기 위해 `compareTo()` 메서드를 제공한다.

```java
Character chK = Character.valueOf('K');
assertTrue(chK.compareTo(chK) == 0);

Character chG = Character.valueOf('G');
assertTrue(chK.compareTo(chG) > 0);

Character chH = Character.valueOf('H');
assertTrue(chG.compareTo(chH) < 0);
```

여기서는 생성자가 Java 9부터 사용되지 않기 때문에 `valueOf()` 메서드를 사용하여 Character 객체를 생성 했다.

### 2) Object.equals() 메서드 사용
또한 객체를 비교하는 일반적인 방법중 하나는 `equals()` 메서드를 사용하는 것이다. 두 객체가 같으면 tru를 반환하고 그렇지 않으면 false를 반환한다.

문자를 비교하는 데 사용하는 방법이다.

```java
Character chL = 'L';
assertTrue(chL.equals(chL));

Character chV = 'V';
assertFalse(chL.equals(chV));
```

### 3) Objects.equals() 메서드 사용
Objects 클래스는 개체 작업을 위한 유틸리티 메서드로 구성된다. `equals()` 메서드를 통해 문자 객체를 비교하는 또 다른 방법을 제공한다.

```java
Character chA = 'A';
Character chB = 'B';

assertTrue(Objects.equals(chA, chA));
assertFalse(Objects.equals(chA, chB));
```

`equals()` 메서드는 문자 객체가 서로 같으면 true를 반환하고 그렇지 않으면 false를 반환한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-compare-characters](https://www.baeldung.com/java-compare-characters)
