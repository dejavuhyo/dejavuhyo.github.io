---
title: Java 문자열 연결
author: dejavuhyo
date: 2022-01-14 10:05:00 +0900
categories: [Language, Java]
tags: [java-strings-concatenation, strings-concatenation, java-strings, 자바-문자열-열결, 문자열-연결, 자바-문자열]
---

## 1. StringBuilder
이 클래스는 문자열 조작을 쉽게 수행할 수 있는 문자열 작성 유틸리티를 제공한다.

StringBuilder 클래스를 사용하여 문자열 연결의 간단한 예를 작성한다.

```java
StringBuilder stringBuilder = new StringBuilder(100);

stringBuilder.append("Baeldung");
stringBuilder.append(" is");
stringBuilder.append(" awesome");

assertEquals("Baeldung is awesome", stringBuilder.toString());
```

내부적으로 StringBuilder는 변경 가능한 문자 배열을 유지 관리한다. 코드 샘플에서 StringBuilder 생성자를 통해 초기 크기를 100으로 선언했다. 이 크기 선언 때문에 StringBuilder는 Strings를 연결 하는 매우 효율적인 방법이 될 수 있다.

StringBuffer 클래스는 StringBuilder의 동기화된 버전이라는 점도 주목할 필요가 있다.

동기화는 종종 스레드 안전성과 동의어이지만 StringBuffer의 빌더 패턴으로 인해 다중 스레드 응용 프로그램에서 사용하는 것은 권장되지 않는다. 동기화된 메서드에 대한 개별 호출은 스레드로부터 안전하지만 [다중 호출](https://dzone.com/articles/why-synchronized-stringbuffer)은 그렇지 않다.

## 2. 덧셈 연산자
이것은 숫자를 더하는 것과 동일한 연산자이며 문자열에 적용될 때 연결하기 위해 오버로드된다.

이것이 어떻게 작동하는지 간단히 살펴본다.

```java
String myString = "The " + "quick " + "brown " + "fox...";

assertEquals("The quick brown fox...", myString);
```

언뜻보기에 이것은 StringBuilder 옵션 보다 훨씬 간결해 보일 수 있다. 그러나 소스 코드가 컴파일될 때 ```+``` 기호는 StringBuilder.append() 호출 체인으로 변환된다. 이 때문에 StringBuilder와 ```+``` 연결 방법을 혼합하는 것은 나쁜 습관으로 간주된다.

또한 루프 내에서 ```+``` 연산자를 사용하는 문자열 연결은 피해야 한다. 문자열 객체가 불변이기 때문에, 연결을 호출할 때마다 새로운 문자열 오브젝트가 생성된다.

## 3. String 메서드
문자열 클래스 자체는 문자열을 연결하기 위한 전체 메소드 호스트를 제공한다.

### 1) String.concat
String.concat 메서드는 String 객체를 연결할 때 가장 먼저 호출하는 포트이다. 이 메서드는 String 개체를 반환하므로 메서드를 함께 결합하는 것이 유용한 기능이다.

```java
String myString = "Both".concat(" fickle")
  .concat(" dwarves")
  .concat(" jinx")
  .concat(" my")
  .concat(" pig")
  .concat(" quiz");

assertEquals("Both fickle dwarves jinx my pig quiz", myString);
```

이 예에서, 우리의 체인은 String 리터럴로 시작되며, concat 메소드는 더 많은 Strings을 추가하기 위한 호출을 체인화하도록 합니다.

### 2) String.format
문자열 템플릿에 다양한 Java Objects를 삽입할 수 있다.

String.format 메서드 시그니처는 템플릿을 나타내는 단일 문자열을 사용한다. 이 템플릿에는 다양한 개체가 배치되어야 하는 위치를 나타내는 ```%``` 문자가 포함되어 있다.

템플릿이 선언되면 템플릿에 주입되는 varargs Object 배열이 필요하다.

간단한 예제를 통해 이 기능이 어떻게 작동하는지 살펴본다.

```java
String myString = String.format("%s %s %.2f %s %s, %s...", "I",
  "ate",
  2.5056302,
  "blueberry",
  "pies",
  "oops");

assertEquals("I ate 2.51 blueberry pies, oops...", myString);
```

위에서 볼 수 있듯이 메서드는 문자열을 올바른 형식으로 주입했다.

### 3) String.join (Java 8+)
만약 애플리케이션이 자바 8 이상에서 실행된다면, String.join 메서드를 이용할 수 있다. 이를 통해 공백이 누락되지 않도록 공통 구분 기호를 사용하여 문자열 배열을 결합 할 수 있다.

```java
String[] strings = {"I'm", "running", "out", "of", "pangrams!"};

String myString = String.join(" ", strings);

assertEquals("I'm running out of pangrams!", myString);
```

이 방법의 큰 장점은 문자열 사이의 구분 기호에 대해 걱정할 필요가 없다는 것이다.

## 4. StringJoiner (Java 8+)
StringJoiner는 모든 String.join 기능을 사용하기 쉬운 클래스로 추상화한다. 생성자는 선택적 접두사와 접미사로 구분된다. 잘 명명된 add 메소드를 사용하여 문자열을 추가할 수 있다.

```java
StringJoiner fruitJoiner = new StringJoiner(", ");

fruitJoiner.add("Apples");
fruitJoiner.add("Oranges");
fruitJoiner.add("Bananas");

assertEquals("Apples, Oranges, Bananas", fruitJoiner.toString());
```

String.join 메서드 대신 이 클래스를 사용하여 프로그램이 실행될 때 문자열을 추가할 수 있다. 배열을 먼저 생성할 필요가 없다.

## 5. Arrays.toString
배열 항목에서는 Array 클래스에는 객체 배열을 멋지게 포맷하는 편리한 toString 메서드가 포함되어 있다. Arrays.toString 메소드는 밀폐된 객체의 toString 메소드도 호출하므로 정의된 객체가 있는지 확인해야 한다.

```java
String[] myFavouriteLanguages = {"Java", "JavaScript", "Python"};

String toString = Arrays.toString(myFavouriteLanguages);

assertEquals("[Java, JavaScript, Python]", toString);
```

안타깝게도 Arrays.toString 메서드는 사용자 정의할 수 없으며 대괄호로 묶인 문자열만 출력한다.

## 6. Collectors.joining (Java 8+)
Stream의 출력을 단일 String으로 퍼널링할 수 있는 Collectors.joining 메서드이다.

```java
List<String> awesomeAnimals = Arrays.asList("Shark", "Panda", "Armadillo");

String animalString = awesomeAnimals.stream().collect(Collectors.joining(", "));

assertEquals("Shark, Panda, Armadillo", animalString);
```

스트림을 사용하면 필터링, 매핑, 반복 등과 같은 Java 8 Stream API 와 관련된 모든 기능이 잠금 해제된다.

## [출처 및 참고]
* <https://www.baeldung.com/java-strings-concatenation>
