---
title: Java StringBuffer 생성자
author: dejavuhyo
date: 2025-02-11 21:50:00 +0900
categories: [Language, Java]
tags: [java-stringbuffer, stringbuffer-constructors, 자바-스트링버퍼, 스트링버퍼-생성자]
---

## 1. StringBuffer 생성자
StringBuffer 클래스에는 4개의 생성자가 있다.

* `StringBuffer()`: 최대 16자를 저장할 수 있는 빈 문자열 버퍼를 생성한다.

* `StringBuffer(int capacity)`: 지정된 문자 용량으로 빈 문자열 버퍼를 생성한다. 이는 문자열 버퍼에 필요한 용량을 알고 있을 때 용량을 늘리는데 시간을 절약하는데 유용하다.

* `StringBuffer(String str)`: 문자열 인수와 동일한 문자를 갖는 문자열 버퍼를 생성한다. 문자열 버퍼의 초기 용량은 "str의 길이 + 16"이다.

* `StringBuffer(CharSequence seq)`: CharSequence와 동일한 문자로 문자열 버퍼를 생성한다. 문자열 버퍼의 초기 용량은 "seq의 길이 + 16"이다.

위의 생성자를 사용하여 StringBuffer 객체를 만드는 몇 가지 예이다.

```java
public class JavaStringBuffer {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer();
        System.out.println(sb.capacity());
        System.out.println(sb);

        StringBuffer sb1 = new StringBuffer(1000);
        System.out.println(sb1.capacity());
        System.out.println(sb1);


        StringBuffer sb2 = new StringBuffer("Hello");
        System.out.println(sb2.capacity());
        System.out.println(sb2);

        CharSequence seq = new StringBuilder("Hello");
        StringBuffer sb3 = new StringBuffer(seq);
        System.out.println(sb3.capacity());
        System.out.println(sb3);
    }
}
```

## 2. Java StringBuffer 용량

* StringBuffer는 바이트 배열을 사용하여 문자를 저장한다.

* StringBuffer를 초기화하면 바이트 배열도 초기화된다.

* 바이트 배열의 크기는 생성자를 통해 지정할 수 있다.

* 바이트 배열의 기본 용량은 16이다.

* 기존 용량보다 더 많은 문자를 문자열 버퍼에 추가하면 바이트 배열은 더 큰 용량과 기존 콘텐츠로 다시 초기화된다.

* StringBuffer에 필요한 용량에 대한 아이디어가 있다면 생성자를 사용하여 필요한 초기 용량을 지정한다. 바이트 배열 재셔플이 필요하지 않기 때문에 StringBuffer 작업이 더 빨라진다.

* StringBuffer의 현재 용량은 해당 `capacity()` 메서드를 호출하여 얻을 수 있다.

## 3. Java StringBuffer에서 문자열로 변환
StringBuffer에서 `toString()` 메서드를 호출하여 문자열 표현을 얻을 수 있다.

```java
public class JavaStringBuffer {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer("Hello");
        String str = sb.toString();
        System.out.println(str);
    }
}
```

## 4. Java StringBuffer 메서드
StringBuffer 메서드는 다음과 같은 범주로 나눌 수 있다.

* `append()`: StringBuffer에 원시 및 객체를 추가하는 여러 오버로드된 메서드. 데이터는 항상 문자열 버퍼의 끝에 추가된다.

* `insert()`: 주어진 인덱스에 문자를 삽입하는데 사용된다. 특정 유형의 인수에 대해 여러 개의 오버로드된 메서드가 있다.

* `delete()`: 문자열 버퍼에서 문자를 삭제하는데 사용된다.

* `replace()`: 문자열 버퍼에 있는 문자를 바꾼다.

* `reverse()`: 문자 시퀀스를 역순으로 바꾼다. String 클래스에는 `reverse()` 메서드가 없기 때문에 문자열을 역순으로 바꾼다.

* `기타 메서드`: `length()`, `capacity()`, `compareTo()`, `trimToSize()`, `substring()`, `indexOf()` 등이 있다.

### 1) StringBuffer append()
문자열 버퍼에 기본 데이터 유형을 추가하는 `append()` 메서드가 있다. `append(Object)` 메서드를 호출하여 객체 문자열 표현을 문자열 버퍼에 추가할 수 있다.

인수가 객체인 경우, `toString()` 메서드를 사용하여 문자열 표현을 가져온다. 문자열 버퍼에 문자 배열을 추가하는 방법이 있습니다.

StringBuffer append() 메서드의 예이다.

```java
public class JavaStringBuffer {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer();

        // append primitive data types
        sb.append('a'); // append char
        sb.append(true); // append boolean
        sb.append(10); // append int
        sb.append(12345L); // append long
        sb.append(10.20); // append double
        sb.append(10.20f); // append float
        System.out.println(sb.toString());

        // append char array
        char[] charArray = {'a', 'b', 'c', 'd'};
        sb.append(charArray); // append complete char array
        sb.append(charArray, 1, 2); // will append index 1 and 2
        System.out.println(sb);

        // append Objects
        sb.append(new Object());
        System.out.println(sb);

        // append String, CharSequence
        sb.append("Hello");
        sb.append(new StringBuilder("Hi"));
        sb.append(new StringBuilder("JavaString"), 4, 10);
        System.out.println(sb);
    }
}
```

### 2) StringBuffer insert()
`append()` 메서드와 마찬가지로 여러 개의 오버로드된 `insert()` 메서드가 있다.

첫 번째 인수는 두 번째 인수가 삽입될 인덱스를 정의한다. 인덱스 인수는 0보다 크거나 같아야 하고, 문자열 버퍼의 길이보다 작거나 같아야 한다. 그렇지 않으면 StringIndexOutOfBoundsExceptionthrow 된다.

`insert()` 메서드를 사용하는 몇 가지 예이다.

```java
public class JavaStringBuffer {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer();

        // insert primitive data types
        sb.insert(0, 'a'); // insert char
        sb.insert(1, true); // insert boolean
        sb.insert(2, 10); // insert int
        sb.insert(3, 12345L); // insert long
        sb.insert(4, 10.20); // insert double
        sb.insert(5, 10.20f); // insert float
        System.out.println(sb.toString());

        // insert char array
        char[] charArray = {'a', 'b', 'c', 'd'};
        sb.insert(6, charArray); // insert complete char array
        sb.insert(7, charArray, 1, 2); // will insert index 1 and 2
        System.out.println(sb);

        // insert Objects
        sb.insert(8, new Object());
        System.out.println(sb);

        // insert String, CharSequence
        sb.insert(9, "Hello");
        sb.insert(10, new StringBuilder("Hi"));
        sb.insert(11, new StringBuilder("JavaString"), 4, 10);
        System.out.println(sb);
    }
}
```

### 3) StringBuffer delete()
StringBuffer에는 문자를 삭제하는 두 가지 방법이 있다.

* `delete(int start, int end)`: 인덱스 start부터 end까지 문자 시퀀스를 삭제한다.

* `deleteCharAt(int index)`: 지정된 인덱스에 있는 문자를 삭제한다.

메서드의 사용법이다.

```java
public class JavaStringBuffer {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer("Hello World 2019");
        sb.delete(6, 12);
        System.out.println(sb);

        sb.deleteCharAt(5);
        System.out.println(sb);
    }
}
```

### 4) StringBuffer replace()
replace 메서드 구문은 다음과 같다. `replace(int start, int end, String str)`

시작 및 종료 매개변수는 str 콘텐츠로 대체될 문자열 버퍼의 인덱스를 정의한다. 먼저 인덱스 사이의 문자가 제거된다. 그런 다음 지정된 문자열이 시작 인덱스에 삽입된다.

```java
public class JavaStringBuffer {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer("Hello World 2019");
        sb.replace(6, 11, "JavaString");
        System.out.println(sb);
    }
}
```

### 5) StringBuffer reverse()
이 방법은 문자 시퀀스를 역순으로 바꾼다. 매우 유용하다.

```java
public class JavaStringBuffer {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer("Hello Java");
        sb.reverse();
        System.out.println(sb);
    }
}
```

### 6) StringBuffer 기타 메소드
StringBuffer 클래스의 여러 가지 메서드이다.

* `length()`: 문자열 버퍼의 길이를 반환한다.

* capacity()`: 문자열 버퍼의 현재 용량을 반환한다.

* `ensureCapacity(int minimumCapacity)`: 문자열 버퍼 용량이 지정된 최소 용량 이상인지 확인한다.

* `trimToSize()`: 저장 공간 사용량을 줄이려고 시도한다.

* `setLength(int newLength)`: 문자열 버퍼의 길이를 설정한다.

* `charAt(int index)`: 주어진 인덱스에 있는 문자를 반환한다. `String charAt()` 메서드 와 매우 유사하다.

* `getChars(int srcBegin, int srcEnd, char[] dest, int dstBegin)`: 문자열 버퍼의 문자는 문자 배열 인수에 대한 복사본이다.

* `setCharAt(int index, char ch)`: 지정된 인덱스에 지정된 문자를 설정한다.

* `substring(int start)`, `substring(int start, int end)`: 문자열 버퍼에서 하위 문자열을 반환한다.

* `indexOf(String str)`, `indexOf(String str, int fromIndex)`, `lastIndexOf(String str)`, `lastIndexOf(String str, int fromIndex)`: 하위 문자열의 첫 번째 및 마지막 발생에 대한 인덱스를 반환한다.

이러한 방법의 사용법을 보여주는 예이다.

```java
import java.util.Arrays;

public class JavaStringBuffer {
    public static void main(String[] args) {
        StringBuffer sb = new StringBuffer("Hello Java");

        System.out.println("length() = " + sb.length());
        System.out.println("capacity() = " + sb.capacity());

        sb.ensureCapacity(1000);
        System.out.println("New capacity = " + sb.capacity());

        sb.trimToSize();
        System.out.println("New capacity = " + sb.capacity());
        System.out.println("Current length = " + sb.length());

        sb.setLength(50);
        System.out.println("New capacity = " + sb.capacity());
        System.out.println("New length = " + sb.length());

        System.out.println("char at index 4 = " + sb.charAt(4));

        char[] dest = new char[5];
        sb.getChars(2, 7, dest, 0);
        System.out.println("char array from string buffer = " + Arrays.toString(dest));

        sb.setCharAt(5, '-');
        System.out.println(sb);

        System.out.println("Substring = " + sb.substring(7));
        System.out.println("Substring = " + sb.substring(6, 8));

        System.out.println("indexOf() = " + sb.indexOf("ll"));
        System.out.println("indexOf() = " + sb.indexOf("1234"));
    }
}
```

## 5. StringBuffer vs StringBuilder

* StringBuffer는 스레드로부터 안전 하지만 StringBuilder는 스레드로부터 안전하지 않다.

* StringBuffer 메서드는 동기화 문제로 인해 StringBuilder 메서드에 비해 약간 느리다.

* StringBuffer에는 `substring()`, `length()` 등의 추가 메서드가 있다. 이러한 메서드는 String 클래스에 이미 있으므로 필요하지 않다.

* StringBuffer 클래스는 Java 1.0부터 존재한다. StringBuilder는 Java 1.5에서 도입된 새로운 클래스이다.

* StringBuffer는 다중 스레드 환경에 권장되는 반면, StringBuilder는 단일 스레드 환경에 권장된다.

## [출처 및 참고]
* [https://www.javastring.net/java/string/java-stringbuffer-class](https://www.javastring.net/java/string/java-stringbuffer-class)
