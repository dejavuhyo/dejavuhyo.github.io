---
title: Java boolean을 int로 변환
author: dejavuhyo
date: 2022-10-21 11:50:00 +0900
categories: [Language, Java]
tags: [java-boolean-int, boolean-to-int, boolean-int, java-boolean, java-int, wrapper-class, apache-commons, java-data-types, data-types, 자바-boolean-int, 자바-변환, 자바-데이터-타입, 래퍼-클래스]
---

## 1. 데이터 유형
Java에서 정수는 int 기본 데이터 유형 또는 Integer 래퍼 클래스로 표시될 수 있다. 원시 데이터 유형은 2의 보수 인코딩 방법으로 표현되는 32비트 부호 있는 정수이다. Integer 클래스는 부호 없는 정수 연산을 수행할 수 있는 래퍼 역할을 할 뿐만 아니라 정수(기본) 값을 Generics와 함께 작동하는 개체로 처리할 수 있다.

반면 boolean 값은 메모리에 특정 크기가 없지만, 기본적으로 운영 체제 및 JVM(Java Virtual Machine)이 사용된다. Java의 모든 기본 데이터 유형과 마찬가지로 boolean에는 boolean 값이 객체처럼 작동하도록 허용하는 boolean 래퍼 클래스가 있다.

데이터 변환을 수행하기 위해 두 데이터 유형(boolean 및 int)의 기본값과 래퍼 클래스를 활용할 수 있다. true 및 false boolean 값이 각각 1과 0을 나타낸다고 가정하면 변환을 수행하는 여러 접근 방식이 있다.

## 2. 기본 boolean을 int로
기본 boolean 값을 int로 변환하려면 표현식의 조건을 평가하여 반환하려는 정수를 결정한다.

```java
public int booleanPrimitiveToInt(boolean foo) {
    int bar = 0;
    if (foo) {
        bar = 1;
    }
    return bar;
}
```

삼항 연산자를 사용하여 이 함수를 단순화할 수 있다.

```java
public int booleanPrimitiveToIntTernary(boolean foo) {
    return (foo) ? 1 : 0;
}
```

이 접근 방식은 기본 데이터 유형(boolean 및 int)을 사용하여 변환한다. 결과적으로 부울 표현식이 참일 때 1을 얻는다. 그렇지 않으면 메서드는 0을 반환한다.

## 3. Wrapper Class
부울 래퍼 클래스를 사용하여 변환을 수행하는 몇 가지 접근 방식이 있다.

* Boolean 클래스의 Static 메서드를 활용할 수 있다.

* Boolean 개체에서 직접 메서드를 호출할 수 있다.

### 1) Static 메서드
Boolean 클래스에는 다음과 같이 사용할 수 있는 비교 메서드가 있다.

```java
public static int booleanObjectToInt(boolean foo) {
    return Boolean.compare(foo, false);
}
```

정적 비교 메서드는 두 인수가 동일한 값을 갖는 경우 0을 반환한다. 즉, foo가 false이면 비교 결과는 0이 된다. 그렇지 않으면 함수는 첫 번째 인수가 true이고 두 번째 인수가 false일 때 1을 반환한다.

마찬가지로 두 번째 인수를 true로 변경하여 동일한 정적 메서드를 사용할 수 있다.

```java
public static int booleanObjectToIntInverse(boolean foo) {
    return Boolean.compare(foo, true) + 1;
}
```

이번에는 foo가 true이면 비교 메서드가 동일한 값의 두 인수를 평가하여 결과가 0이 된다. 그러나 결과에 1을 추가하면 실제 boolean 변수에서 예상되는 정숫값이 반환된다.

### 2) Boolean Class Object
Boolean 클래스 객체에는 다음과 같이 사용할 수 있는 compareTo와 같은 기능이 있다.

```java
public static int booleanObjectMethodToInt(Boolean foo) {
    return foo.compareTo(false);
}
```

booleanObjectMethodToInt 메서드를 사용하여 정적 메서드에서와 동일한 방식으로 boolean 값을 정수로 변환할 수 있다. 마찬가지로 인수를 true로 변경하고 결과에 1을 추가 하여 반전된 버전을 적용할 수 있다.

## 4. Apache Commons
[Apache Commons](https://search.maven.org/search?q=g:org.apache.commons%20AND%20a:commons-lang3)는 BooleanUtils와 같은 유틸리티 클래스를 제공하는 인기 있는 Java용 오픈 소스 라이브러리이다. 다음과 같이 Maven에서 라이브러리를 종속성으로 추가할 수 있다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.12.0</version>
</dependency>
```

라이브러리가 pom.xml 파일에 있으면 BooleanUtils 클래스를 사용하여 boolean 값을 정수로 변환할 수 있다.

```java
public static int booleanUtilsToInt(Boolean foo) {
    return BooleanUtils.toInteger(foo);
}
```

예제 메서드 booleanPrimitiveToIntTernary와 마찬가지로 내부적으로 toInteger 메서드는 변환을 위해 동일한 삼항 연산자를 수행한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-boolean-to-int](https://www.baeldung.com/java-boolean-to-int)
