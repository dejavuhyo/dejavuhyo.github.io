---
title: Java String Pool
author: dejavuhyo
date: 2024-12-11 10:00:00 +0900
categories: [Language, Java]
tags: [java-string, string-pool, 자바-스트링, 스트링-풀]
---

## 1. String Pool
Java String Pool은 문자열 객체를 저장하기 위한 힙 메모리의 특수 영역이다. 문자열 객체를 만드는 방법은 두 가지가 있다.

* 큰따옴표를 사용하여 문자열 객체를 만들면 문자열 풀에 저장된다.

* new 연산자를 사용하여 문자열을 만들면 힙 메모리에 만들어진다.

## 2. String Pool 동작

* String 리터럴을 생성하면 String Pool에 저장된다.

* String Pool에 같은 값을 가진 문자열이 이미 있는 경우, 새로운 문자열 객체는 생성되지 않는다. 기존 문자열 객체에 대한 참조가 반환된다.

* Java String Pool은 문자열 객체의 캐시이다. 문자열이 불변이기 때문에 가능하다.

* new 연산자를 사용하여 문자열 객체를 생성하면 힙 영역에 생성된다. 문자열 풀로 이동하려면 `intern()` 메서드를 사용할 수 있다.

* String Pool은 Flyweight 디자인 패턴의 좋은 예이다.

## 3. String Pool 예제
String Pool 작동 방식 예제이다.

![java-string-pool](/assets/img/2024-12-11-java-string-pool/java-string-pool.png)

```java
public class JavaStringPool {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = new String("Hi");
        String s4 = "Hi";

        System.out.println("s1 == s2? " + (s1 == s2));
        System.out.println("s3 == s4? " + (s3 == s4));

        s3 = s3.intern();
        System.out.println("s3 == s4? " + (s3 == s4));
    }
}
```

* 첫 번째 문자열 s1을 만들 때 String Pool에 "Hello" 값을 가진 문자열이 없다. 따라서 문자열 "Hello"가 Pool에 생성되고 해당 참조가 s1에 할당된다.

* 두 번째 문자열 s2를 생성할 때 String Pool에 이미 "Hello" 값을 가진 문자열이 있다. 기존 문자열 참조는 s2에 할당된다.

* 세 번째 문자열 s3를 생성할 때는 new 연산자를 사용했기 때문에 힙 영역에 생성된다.

* 네 번째 문자열 s4를 생성하면 "Hi" 값을 갖는 새 문자열이 Pool에 생성되고 해당 문자열의 참조가 s4에 할당된다.

* `s1 == s2`를 비교하면 두 변수가 동일한 문자열 객체를 가리키기 때문에 참으로 반환된다.

* `s3 == s4`를 비교하면 서로 다른 문자열 객체를 가리키기 때문에 거짓으로 반환된다.

* s3에서 `inter()` 메서드를 호출하면 풀에 "Hi" 값을 가진 문자열이 있는지 확인한다. 이미 풀에 이 값을 가진 문자열이 있으므로 해당 문자열의 참조가 반환되어 s3에 할당된다.

* 이제 `s3 == s4는` 참이다. 왜냐하면 두 변수가 동일한 문자열 객체를 가리키고 있기 때문이다.

## 4. String Pool의 이점
자바 문자열 풀의 이점 중 일부이다.

* Java String Pool은 문자열 객체의 캐싱을 허용한다. 이는 다른 객체에서 사용할 수 있는 JVM의 많은 메모리를 절약한다.

* Java String Pool은 재사용성으로 인해 애플리케이션의 성능을 개선하는데 도움이 된다. 풀에 동일한 값을 가진 문자열이 이미 있는 경우 새 문자열을 만드는 데 걸리는 시간을 절약한다.

## 5. new 연산자 문자열 생성 문제
new 연산자를 사용하여 문자열을 생성하면 안되는 이유이다.

```java
String s3 = new String("Hi");
```

* 문자열 풀에 "Hi" 값을 갖는 새 문자열이 생성된다.

* 그런 다음 위 단계에서 생성한 문자열을 전달하여 String 생성자가 호출된다.

* 힙 공간에 새로운 문자열이 생성되고 해당 문자열의 참조가 s3에 할당된다.

* 첫 번째 단계에서 생성된 문자열은 고아가 되어 가비지 수집의 대상이 된다.

* 힙 영역에 단일 문자열을 생성하려고 했는데 결국 두 개의 문자열을 생성하게 되었다.

## [출처 및 참고]
* [https://www.javastring.net/java/string/pool](https://www.javastring.net/java/string/pool)
