---
title: Java StringBuilder 생성자
author: dejavuhyo
date: 2025-02-18 22:20:00 +0900
categories: [Language, Java]
tags: [java-stringbuilder, stringbuilder-constructors, 자바-스트링빌더, 스트링빌더-생성자]
---

## 1. StringBuilder 생성자
StringBuilder 클래스에는 4개의 생성자가 있다.

* `StringBuilder()`: 최대 16자를 입력할 수 있는 빈 문자열 빌더를 생성한다.

* `StringBuilder(int capacity)`: 지정된 문자 용량으로 빈 문자열 빌더를 생성한다. 이는 문자열 빌더에 필요한 용량을 알고 있을 때 용량을 늘리는데 시간을 절약하는데 유용하다.

* `StringBuilder(String str)`: 문자열 인수와 동일한 문자를 갖는 새로운 문자열 빌더를 생성한다. 문자열 빌더의 초기 용량은 "str의 길이 + 16"이다.

* `StringBuilder(CharSequence seq)`: CharSequence와 동일한 문자로 새 문자열 빌더를 만든다. 문자열 빌더의 초기 용량은 "seq의 길이 + 16"이다.

위의 생성자를 사용하여 StringBuilder 객체를 만드는 몇 가지 예이다.

```java
public class JavaStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        System.out.println(sb.capacity());
        System.out.println(sb);

        StringBuilder sb1 = new StringBuilder(1000);
        System.out.println(sb1.capacity());
        System.out.println(sb1);

        StringBuilder sb2 = new StringBuilder("Java");
        System.out.println(sb2.capacity());
        System.out.println(sb2);

        CharSequence seq = new StringBuilder("String");
        StringBuilder sb3 = new StringBuilder(seq);
        System.out.println(sb3.capacity());
        System.out.println(sb3);
    }
}
```

## 2. StringBuilder 용량

* StringBuilder는 바이트 배열을 사용하여 문자를 저장한다.

* StringBuilder를 초기화하면 바이트 배열도 초기화된다.

* 바이트 배열의 크기는 생성자를 통해 지정할 수 있다.

* 바이트 배열의 기본 용량은 16이다.

* 기존 용량보다 많은 문자를 문자열 빌더에 추가하면 바이트 배열은 더 큰 용량과 기존 콘텐츠로 다시 초기화된다.

* StringBuilder에 필요한 용량에 대한 아이디어가 있다면 생성자를 사용하여 필요한 초기 용량을 지정한다. 바이트 배열 재셔플이 필요하지 않기 때문에 StringBuilder 작업이 더 빨라진다.

## 3. StringBuilder에서 문자열로
StringBuilder에서 `toString()` 메서드를 호출하여 문자열 표현을 얻을 수 있다.

```java
public class JavaStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello");
        String str = sb.toString();
        System.out.println(str);
    }
}
```

## 4. StringBuilder 메서드
StringBuilder 메서드는 다음과 같은 범주로 나눌 수 있다.

* `append()`: StringBuilder에 원시형과 객체를 추가하는 여러 오버로드된 메서드. 데이터는 항상 문자열 빌더의 끝에 추가된다.

* `insert()`: 주어진 인덱스에 문자를 삽입하는데 사용된다. 특정 유형의 인수에 대해 여러 개의 오버로드된 메서드가 있다.

* `delete()`: 문자열 빌더에서 문자를 삭제하는데 사용된다.

* `replace()`: 문자열 버퍼에 있는 문자를 바꾼다.

* `reverse()`: 문자 시퀀스를 역순으로 바꾼다. String 클래스에는 `reverse()` 메서드가 없기 때문에 문자열을 역순으로 바꾼다.

* 기타 메서드: `compareTo()`, `indexOf()`, `lastIndexOf()`

### 1) StringBuilder append()
문자열 빌더에 기본 데이터 유형을 추가하는 `append()` 메서드가 있다. `append(Object)` 메서드를 호출하여 문자열 빌더에 객체 문자열 표현을 추가할 수 있다.

인수가 객체인 경우, `toString()` 메서드를 사용하여 문자열 표현을 가져온다. 문자열 빌더에 문자 배열을 추가하는 방법이 있다.

```java
public class JavaStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();

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

### 2) StringBuilder insert()
`append()` 메서드와 마찬가지로 여러 개의 오버로드된 `insert()` 메서드가 있다.

첫 번째 인수는 두 번째 인수가 삽입될 인덱스를 정의한다. 인덱스 인수는 0보다 크거나 같아야 하고, 문자열 빌더의 길이보다 작거나 같아야 한다. 그렇지 않으면 StringIndexOutOfBoundsException이 throw된다.

```java
public class JavaStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();

        // insert primitive data types
        sb.insert(0, 'a'); // insert char
        sb.insert(1, 10); // insert int
        sb.insert(1, 10.20f); // insert float
        System.out.println(sb.toString());

        // insert char array
        char[] charArray = {'1', '2', '3'};
        sb.insert(1, charArray); // insert complete char array
        sb.insert(3, charArray, 1, 2); // will insert index 1 and 2
        System.out.println(sb);

        // insert String, CharSequence
        sb.insert(9, "Java");
        sb.insert(10, new StringBuilder("String"));
        sb.insert(11, new StringBuilder("Developer"), 4, 6);
        System.out.println(sb);
    }
}
```

### 3) StringBuilder delete()
StringBuilder에는 문자를 삭제하는 두 가지 방법이 있다.

* `delete(int start, int end)`: 인덱스 start부터 end까지 문자 시퀀스를 삭제한다.

* `deleteCharAt(int index)`: 지정된 인덱스에 있는 문자를 삭제한다.

StringBuilder의 delete() 메서드를 사용하는 방법이다.

```java
public class JavaStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello World");
        sb.delete(6, 11);
        System.out.println(sb);
        sb.deleteCharAt(4);
        System.out.println(sb);
    }
}
```

### 4) StringBuilder replace()
replace 메서드 구문은 다음과 같다. `replace(int start, int end, String str)`

시작 및 종료 매개변수는 str 콘텐츠로 대체될 문자열 버퍼의 인덱스를 정의한다. 먼저 인덱스 사이의 문자가 제거된다. 그런 다음 지정된 문자열이 시작 인덱스에 삽입된다.

```java
public class JavaStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello World 2019");
        System.out.println(sb);
        sb.replace(6, 11, "JavaString");
        System.out.println(sb);
    }
}
```

### 5) StringBuilder reverse()
이 방법은 문자 시퀀스를 역순으로 바꾼다. 매우 유용하다.

```java
public class JavaStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello");
        sb.reverse();
        System.out.println(sb);
    }
}
```

### 6) StringBuilder 기타 메서드

* `int compareTo(StringBuilder another)`: 두 StringBuilder 인스턴스를 사전식으로 비교하는데 사용된다.

* `int indexOf(String str)`, `indexOf(String str, int fromIndex)`: 문자열 빌더에서 문자열의 첫 번째 발생을 찾는데 사용된다.

* `int lastIndexOf(String str)`: 문자열 빌더에서 하위 문자열이 나타나는 마지막 인덱스를 찾는데 사용된다.

* `int lastIndexOf(String str, int fromIndex)`: 지정된 인덱스에서 시작하여 뒤로 검색하여 지정된 하위 문자열의 마지막 발생 위치의 인덱스를 반환한다.

```java
public class JavaStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello Jello Trello");
        StringBuilder sb1 = new StringBuilder("Hello Trello");

        System.out.println(sb.compareTo(sb1));

        System.out.println(sb.indexOf("ll"));
        System.out.println(sb.indexOf("ll", 6));

        System.out.println(sb.lastIndexOf("o"));
        System.out.println(sb.lastIndexOf("o", 6));
    }
}
```

## 4. StringBuilder vs StringBuffer

* StringBuilder는 스레드로부터 안전하지 않지만 StringBuffer는 스레드로부터 안전하다.

* StringBuilder 메서드는 동기화가 없기 때문에 StringBuffer 메서드보다 빠르다.

* StringBuilder에는 StringBuilder 클래스에 있는 쓸모없는 메서드가 없다. 예를 들어, `substring()`, `length()` 등.

* StringBuilder는 Java 1.5에서 도입되었고, StringBuffer는 Java 1.0부터 존재한다.

* 단일 스레드 문자열 조작에는 StringBuilder가 권장되고, 다중 스레드 문자열 조작에는 StringBuffer가 권장된다.

## [출처 및 참고]
* [https://www.javastring.net/java/string/java-stringbuilder-class](https://www.javastring.net/java/string/java-stringbuilder-class)
