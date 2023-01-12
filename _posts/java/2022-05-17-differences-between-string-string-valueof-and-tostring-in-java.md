---
title: Java String, toString, String.valueOf 차이점
author: dejavuhyo
date: 2022-05-17 10:20:00 +0900
categories: [Language, Java]
tags: [java-try-catch-vs-if-else, try-catch-vs-if-else, java-try-catch, java-if-else, try-catch-if-else-차이점]
---

## 1. String, toString
이것은 객체를 String 유형 값으로 변환하는 표준 유형 변환이다. 이 방법을 사용할 때 유형이 String 유형으로 변환 가능해야 한다는 점에 유의해야 한다.

따라서 instanceof를 사용하여 변환할 수 있는지 여부를 확인하는 형식 검사를 수행하는 것이 가장 좋다. 그렇지 않으면 CalssCastException이 발생하기 쉽다.

또한 Object 유형으로 정의된 객체가 String으로 변환될 때 구문 검사에서 오류를 보고하지 않아 잠재적인 오류가 발생할 수 있으므로 특히 주의해야 한다.

```java
Object obj = new Integer(100);
String strVal = (String)obj;
```

Integer type에서 String type으로 캐스트 할 수 없으므로 런타임에 오류가 발생합니다. java.lang.Object 클래스에 이미 .toString()이라는 public 메소드가 있기 때문이다. 따라서 이 메소드는 엄격한 의미에서 모든 Java 객체에서 호출될 수 있다.

그러나 사용할 때는 개체가 null 값이 아닌지 확인해야 합니다. 그렇지 않으면 NullPointerException이 발생한다. 이 메서드를 사용할 때 일반적으로 파생 클래스는 Object의 toString() 메서드를 재정의한다.

## 2. String.valueOf
메소드는 정적 메서드이며 String을 통해 직접 호출할 수 있다. 그것은 완벽하다고 할 수 있다.

다만 이런 식으로 쓰는 것이 익숙하지 않을 뿐입니다. 이 구현은 이전 두 가지의 단점과 단점을 피한다.

```java
public static String valueOf(Object obj){
    return (obj==null) ? "null" : obj.toString()
}
```

내부적으로는 null로 판단되어 null 포인터 예외가 보고되지 않는다. 위의 소스 코드에서 null 값에 대해 걱정할 필요가 없는 이유를 명확하게 알 수 있다.

그러나 이것은 또한 우리에게 숨겨진 위험을 제공한다. object가 null일 때 String.valueOf(object)의 값은 null이 아니라 "null" 문자열이라는 점에 유의해야 한다!. 사용 중 주의해야 한다.

```java
if(String.valueOf(object)==null){
    System.out.println("The value passed in is null!");
}
```

그러한 진술에 어떤 문제가 발생할 수 있다. 콘솔에 출력할 때 시각적으로 다음 명령문의 실행 결과의 차이점은 무엇인가?

```java
System.out.println(String.valueOf(null));
System.out.println(null);
```

우리가 보는 출력은 정확히 같은 것이 될 것이다. null, 그러나 그것들이 같은 것을 의미하지는 않는다.

> **요약**: 이 세 가지를 사용하려면 `String.valueOf()`를 사용해야 한다고 생각한다. 이러한 사용은 안전하고 신뢰할 수 있으며 이상을 일으키지 않는다.

## 3. Exception 참고

```java
int intNum = 1;
Object objNull = null;
Object objnum = new Integer(1);

// toString
String str1 = intNum.toString(intNum); // 컴파일 에러
String str2 = objNull.toString();      // NullPointerException
String str3 = objnum.toString();

// (String)
String str4 = (String) intNum;         // 컴파일 에러
String str5 = (String) objNull;
String str6 = (String) objnum;         // ClassCastException

// String.valueOf()
String str7 = String.valueOf(intNum);
String str8 = String.valueOf(objNull);
String str9 = String.valueOf(objnum);
```

## [출처 및 참고]
* [https://blog.actorsfit.com/a?ID=01600-a77a8bae-c217-49e6-a5d5-4cca3d7fa7c2](https://blog.actorsfit.com/a?ID=01600-a77a8bae-c217-49e6-a5d5-4cca3d7fa7c2)
* [https://reference-m1.tistory.com/247](https://reference-m1.tistory.com/247)
* [https://nocount.tistory.com/196](https://nocount.tistory.com/196)
