---
title: Java String Pool
author: dejavuhyo
date: 2022-01-25 09:30:00 +0900
categories: [Language, Java]
tags: [java-string-pool, string-pool, 자바-스트링-풀, 스트링-풀]
---

## 1. 자바 스트링 풀
자바 스트링 풀은 JVM에 의해 스트링이 저장되는 특별한 메모리 영역 이다.

## 2. String Interning
Java에서 문자열의 불변성 덕분에 JVM은 풀에 각 리터럴 문자열의 복사본을 하나만 저장 하여 문자열에 할당된 메모리 양을 최적화할 수 있다. 이 과정을 interning이라고 한다.

String 변수를 만들고 값을 할당하면 JVM은 풀에서 동일한 값의 String을 검색한다.

발견되면 Java 컴파일러는 추가 메모리를 할당하지 않고 단순히 메모리 주소에 대한 참조를 반환한다.

찾을 수 없으면 풀에 추가되고(interned) 해당 참조가 반환된다.

이를 확인하기 위한 테스트 이다.

```java
String constantString1 = "Baeldung";
String constantString2 = "Baeldung";
        
assertThat(constantString1)
  .isSameAs(constantString2);
```

## 3. 생성자를 사용하여 할당된 문자열
new 연산자를 통해 문자열을 생성하면 Java 컴파일러는 새 객체를 생성하고 JVM용으로 예약된 힙 공간에 저장한다.

이와 같이 생성된 모든 문자열은 고유한 주소를 가진 다른 메모리 영역을 가리킨다.

이것이 이전의 경우와 어떻게 다른지 확인한다.

```java
String constantString = "Baeldung";
String newString = new String("Baeldung");
 
assertThat(constantString).isNotSameAs(newString);
```

## 4. String Literal과 String Object
new() 연산자를 사용하여 String 객체를 생성하면 항상 힙 메모리에 새 객체를 생성한다. 반면에 "Baeldung"과 같은 문자열 리터럴 구문을 사용하여 객체를 생성하면 문자열 풀에서 이미 존재하는 객체를 반환할 수 있다. 그렇지 않으면 새 String 개체를 만들고 나중에 다시 사용할 수 있도록 문자열 풀에 넣는다.

상위 수준에서 둘 다 String 객체이지만 주요 차이점은 new() 연산자가 항상 새로운 String 객체를 생성한다는 점이다. 또한 리터럴을 사용하여 문자열을 생성할 때 문자열이 interned 된다.

이것은 String 리터럴과 new 연산자를 사용하여 생성된 두 개의 String 객체를 비교할 때 훨씬 더 명확해진다.

```java
String first = "Baeldung"; 
String second = "Baeldung"; 
System.out.println(first == second); // True
```

이 예에서 String 객체는 동일한 참조를 갖는다.

다음으로 new를 사용하여 두 개의 서로 다른 객체를 만들고 서로 다른 참조가 있는지 확인한다.

```java
String third = new String("Baeldung");
String fourth = new String("Baeldung"); 
System.out.println(third == fourth); // False
```

마찬가지로 String 리터럴을 ```==``` 연산자를 사용하여 new() 연산자를 사용하여 생성된 String 객체와 비교하면 false를 반환한다.

```java
String fifth = "Baeldung";
String sixth = new String("Baeldung");
System.out.println(fifth == sixth); // False
```

일반적으로 가능한 경우 String 리터럴 표기법을 사용해야 한다. 읽기가 더 쉽고 컴파일러가 코드를 최적화할 수 있는 기회를 제공한다.

## 5. Manual Interning
intern할 객체에서 intern() 메서드를 호출하여 Java String Pool에서 String을 수동으로 intern할 수 있다.

문자열을 수동으로 내부에 넣으면 해당 참조가 풀에 저장되고 JVM은 필요할 때 이 참조를 반환한다.

이에 대한 테스트 케이스이다.

```java
String constantString = "interned Baeldung";
String newString = new String("interned Baeldung");

assertThat(constantString).isNotSameAs(newString);

String internedString = newString.intern();

assertThat(constantString)
  .isSameAs(internedString);
```

## 6. Garbage Collection
Java 7 이전에는 JVM이 고정된 크기의 PermGen 공간에 Java String Pool을 배치했다. 이 공간은 런타임에 확장할 수 없고 가비지 수집에 적합하지 않다.

(힙 대신) PermGen에서 문자열을 intern하는 위험은 너무 많은 문자열을 intern할 경우 JVM에서 OutOfMemory 오류가 발생할 수 있다는 것 이다.

Java 7부터 Java String Pool은 JVM에 의해 가비지 수집 되는 힙 공간에 저장된다. 이 접근 방식의 장점은 참조되지 않은 문자열이 풀에서 제거되어 메모리가 해제 되기 때문에 OutOfMemory 오류의 위험이 감소한다는 것이다.

## 7. 성능 및 최적화
Java 6에서 수행할 수 있는 유일한 최적화는 MaxPermSize JVM 옵션을 사용하여 프로그램을 호출하는 동안 PermGen 공간을 늘리는 것이다.

```text
-XX:MaxPermSize=1G
```

Java 7에는 풀 크기를 검사하고 확장/축소하는 더 자세한 옵션이 있다. 풀 크기를 보기 위한 두 가지 옵션이다.

```text
-XX:+PrintFlagsFinal
```

```text
-XX:+PrintStringTableStatistics
```

버킷 측면에서 풀 크기를 늘리려면 StringTableSize JVM 옵션을 사용할 수 있다.

```text
-XX:StringTableSize=4901
```

Java 7u40 이전에는 기본 풀 크기가 1009 버킷이었지만 이 값은 최신 Java 버전에서 약간 변경되었다. 정확히는 Java 7u40에서 Java 11까지의 기본 풀 크기는 60013이었으며 이제는 65536으로 증가했다.

풀 크기를 늘리면 더 많은 메모리를 소비하지만 테이블에 문자열을 삽입하는데 필요한 시간을 줄이는 이점이 있다.

## 8. 자바 9에 대한 참고 사항
Java 8까지 문자열은 내부적으로 UTF-16으로 인코딩된 ```char[]``` 문자 배열로 표현되어 모든 문자가 2바이트의 메모리를 사용한다.

Java 9에서는 Compact Strings라는 새로운 표현이 제공된다. 이 새로운 형식은 저장된 내용에 따라 ```char[]```와 ```byte[]``` 사이에서 적절한 인코딩을 선택한다.

새로운 String 표현은 필요할 때만 UTF-16 인코딩을 사용하기 때문에 힙 메모리의 양이 상당히 줄어들고 JVM 에서 Garbage Collector 오버헤드가 줄어든다.

## [출처 및 참고]
* <https://www.baeldung.com/java-string-pool>
