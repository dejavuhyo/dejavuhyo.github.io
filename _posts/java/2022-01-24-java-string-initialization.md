---
title: Java 문자열 초기화
author: dejavuhyo
date: 2022-01-24 09:30:00 +0900
categories: [Language, Java]
tags: [java-string-initialization, string-initialization, initialization, 자바-문자열-초기화, 문자열-초기화, 초기화, 자바-문자열]
---

## 1. Creation
Java에서 String이 생성되는 방식을 알아야 한다.

new 키워드나 리터럴 구문을 사용할 수 있다.

```java
String usingNew = new String("baeldung");
String usingLiteral = "baeldung";
```

## 2. 문자열 선언만
명시적으로 값을 할당하지 않고 String을 선언한다.

로컬 또는 멤버 변수로 이 작업을 수행할 수 있다.

```java
public class StringInitialization {

    String fieldString;

    void printDeclaredOnlyString() {
        String localVarString;
        
        // System.out.println(localVarString); -> compilation error
        System.out.println(fieldString);
    }
}
```

값을 지정하기 전에 localVarString을 사용하려고 하면 컴파일 오류가 발생한다. 반면에 콘솔은 fieldString의 값 에 대해 "null" 을 표시한다.

멤버 변수는 클래스가 생성될 때 기본값으로 초기화되며 String의 경우 null 이다. 그러나 지역 변수를 직접 초기화해야 한다.

localVarString에 null 값 을 지정하면 두 값이 실제로 동일하다는 것을 알 수 있다.

```java
String localVarString = null;
assertEquals(fieldString, localVarString);
```

## 3. 리터럴을 사용한 문자열 초기화
동일한 리터럴을 사용하여 두 개의 String을 생성한다.

```java
String literalOne = "Baeldung";
String literalTwo = "Baeldung";
```

참조를 비교하여 하나의 객체만 생성되었는지 확인한다.

```java
assertTrue(literalOne == literalTwo);
```

그 이유는 String이 풀에 저장 된다는 사실과 관련이 있다. literalOne은 풀에 "baeldung" 문자열을 추가하고 literalTwo는 이를 재사용한다.

## 4. new를 사용한 문자열 초기화
그러나 new 키워드를 사용하면 몇 가지 다른 동작을 볼 수 있다.

```java
String newStringOne = new String("Baeldung");
String newStringTwo = new String("Baeldung");
```

두 String의 값은 이전과 동일하지만 이번에는 다른 객체를 지정해야 한다.

```java
assertFalse(newStringOne == newStringTwo);
```

## 5. 빈 문자열
세 개의 빈 String을 생성한다.

```java
String emptyLiteral = "";
String emptyNewString = new String("");
String emptyNewStringTwo = new String();
```

지금까지 알고 있는 것처럼 emptyLiteral은 String pool에 추가되고 나머지 두 개는 힙에 직접 추가된다.

이들은 동일한 객체가 아니지만 모두 동일한 값을 갖는다.

```java
assertFalse(emptyLiteral == emptyNewString)
assertFalse(emptyLiteral == emptyNewStringTwo)
assertFalse(emptyNewString == emptyNewStringTwo)
assertEquals(emptyLiteral, emptyNewString);
assertEquals(emptyNewString, emptyNewStringTwo);
```

## 6. null 값
null String의 작동 방식이다.

null String을 선언하고 초기화한다.

```java
String nullValue = null;
```

nullValue를 인쇄하면 이전에 본 것처럼 "null"이라는 단어가 표시된다. 그리고 nullValue에 대한 메서드를 호출하려고 하면 NullPointerException이 발생한다.

JVM 사양은 null이 모든 참조에 대한 기본값이라고 말하므로 특별히 String에 연결되지 않는다. 그리고 실제로 사양은 null에 대한 구체적인 값 인코딩을 요구하지 않는다.

그렇다면 String을 인쇄하기 위해 "null"은 어디에서 온것인가?

PrintStream#println 구현을 살펴보면 String#valueOf를 호출하는 것을 볼 수 있다.

```java
public void println(Object x) {
    String s = String.valueOf(x);
    synchronized (this) {
        print(s);
        newLine();
    }
}
```

그리고 ```String#valueOf```를 보면 다음과 같은 답을 얻을 수 있다.

```java
public static String valueOf(Object obj) {
    return (obj == null) ? "null" : obj.toString();
}
```

이것이 "null"의 이유이다.

## [출처 및 참고]
* <https://www.baeldung.com/java-varargs>
