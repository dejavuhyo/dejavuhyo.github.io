---
title: Java String 비교
author: dejavuhyo
date: 2024-12-13 08:30:00 +0900
categories: [Language, Java]
tags: [java-string, string-comparison, 자바-스트링, 스트링 비교]
---

## 1. equals() 메서드
`equals()` 메서드를 사용하여 두 문자열의 동일성을 비교할 수 있다.

Java 문자열은 대소문자를 구분하므로 비교도 대소문자를 구분한다. 대소문자를 구분하지 않고 동일성을 테스트하려면 `equalsIgnoreCase()`메서드를 사용한다.

* 예제

```java
public class StringComparison {
    public static void main(String[] args) {
        String s1 = "Hello";
        System.out.println(s1);
        System.out.println(s1.equals("Hello"));
        System.out.println(s1.equals("HELLO"));
        System.out.println(s1.equalsIgnoreCase("HELLO"));
    }
}
```

* 결과

```text
Hello
true
false
true
```

## 2. == 연산자
Java `==` 연산자는 두 변수가 같은 객체를 참조하는 경우 true를 반환한다. Java String은 변경 불가능하므로 `==`를 사용하여 동등성을 확인할 필요가 없다. 항상 `equals()`해당 메서드를 사용해야 한다.

문자열 비교를 위해 `==` 연산자를 사용하는 예제이다.

* 예제

```java
public class StringComparison {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = new String("Hello");

        System.out.println(s1 == s2); // true
        System.out.println(s1 == s3); // false
    }
}
```

* 결과

```text
true
false
```

위의 예제에서 모든 문자열 객체는 같은 값을 갖는다. 문자열 s1과 s2는 같은 객체를 참조하는 pool에 있다. 문자열 s3는 new 연산자 때문에 힙 공간에서 생성된다. 그래서 `s1 == s2`은 true이고 `s1 == s3`은 false 이다.

## 3. compareTo() 메서드
이 방법은 두 문자열을 사전식으로 비교한다. 출력은 항상 정수 값이다.

비교 규칙에 따라 달라진다. 문자열 클래스는 Comparable인터페이스를 구현한다. 이것은 문자열 배열이나 컬렉션을 정렬하려고 할 때 사용되는 방법이다.

* 문자열이 인수 문자열보다 앞에 오면 출력은 음수이다.

* 두 문자열의 문자는 하나씩 비교된다.

* 어떤 인덱스의 문자가 일치하지 않으면, 그 코드 포인트의 차이가 출력이다. 따라서 출력은 인덱스에서 문자 'n'이 일치하지 않으면 `this.charAt(n)-argumentString.charAt(n)`이다.

* 문자가 모든 인덱스에서 일치하면 짧은 길이의 문자열이 긴 길이의 문자열보다 앞에 온다. 따라서 출력은 음수이다. 값은 문자열 길이의 차이이다.

* 두 문자열이 같은 값을 가지고 있는 경우 출력은 0이다. 이 경우 두 문자열 사이 `equals()`는 true이여야 한다.

`compareTo()` 메서드의 몇 가지 예제이다.

* 예제

```java
public class StringComparison {
    public static void main(String[] args) {
        System.out.println("Hello".compareTo("Hi"));
        System.out.println("Hi".compareTo("Hi"));
        System.out.println("Hi".compareTo("Hii"));
        System.out.println("Hii".compareTo("Hi"));
        System.out.println("He".compareTo("Hello"));
        System.out.println("Hello".compareTo("HI"));
    }
}
```

* 결과

```text
-4
0
-1
1
-3
28
```

* `"Hello".compareTo("Hi")`: 인덱스 1에서 문자가 다르기 때문에 -4를 반환한다. 'e'의 코드 포인트는 101이고 'i'의 코드 포인트는 105이다. 두 문자의 차이는 -4이다.

* `"Hi".compareTo("Hi")`: 두 문자열이 같으므로 0을 반환한다.

* `"Hi".compareTo("Hii")`: -1을 반환한다. 두 문자열의 모든 인덱스에서 문자는 동일하다. 출력은 "Hi"가 "Hii" 앞에 오기 때문에 음수이다. 출력 값은 길이 차이가 1이기 때문에 1이다.

* `"Hii".compareTo("Hi")`: 1을 반환한다. 출력은 양수이다. "Hii"가 "Hi" 뒤에 오기 때문이다. 길이의 차이는 1이다.

* `"He".compareTo("Hello")`: -3을 반환한다. 위의 논리를 사용하여 출력을 알아낼 수 있다.

* `"Hello".compareTo("HI")`: 28을 반환한다. 'e'의 코드 포인트는 101이고 'I'는 73입니다.

## 4. compareToIgnoreCase() 메서드
이 메서드는 `compareTo()` 메서드와 같은 방식으로 작동한다. 유일한 차이점은 비교가 대소문자를 구분하지 않는다는 것이다. 문자열은 비교 전에 대문자 또는 소문자로 변환된다. 이는 대소문자를 고려하지 않고 문자열 컬렉션을 정렬하려는 경우에 유용하다.

`compareToIgnoreCase()` 메서드의 예제이다.

* 예제

```java
public class StringComparison {
    public static void main(String[] args) {
        System.out.println("H".compareToIgnoreCase("h")); // 0
        System.out.println("HI".compareToIgnoreCase("hi")); // 0
        System.out.println("Hello".compareTo("HI")); // 28
        System.out.println("HI".codePointAt(1)); // 73
        System.out.println("Hello".compareToIgnoreCase("HI")); // -4
    }
}
```

* 결과

```text
0
0
28
73
-4
```

## 5. Collator 클래스
로케일별 문자열 비교를 위해 Collator 클래스를 사용할 수 있다. 문자열을 비교하기 위해 자체 비교 규칙을 정의할 수 있다.

로케일별 비교를 위해 Collator 클래스를 사용하는 간단한 예제이다. 또한 사용자 지정 비교 규칙을 정의하는 방법이다.

* 예제

```java
public class StringComparison {
    public static void main(String[] args) {
        try {
            Collator collator = Collator.getInstance();
            Collator collatorDE = Collator.getInstance(Locale.GERMANY);

            System.out.println(collator.compare("ABC", "B")); // -1
            System.out.println(collatorDE.compare("ABC", "B")); // -1

            String rules = "< B < A";
            RuleBasedCollator rbc = new RuleBasedCollator(rules);

            System.out.println(rbc.compare("ABC", "B")); // 1
            
        } catch (ParseException e) {
            e.printStackTrace();
        }
    }
}
```

* 결과

```text
-1
-1
1
```

## [출처 및 참고]
* [https://www.javastring.net/java/string/java-string-comparison](https://www.javastring.net/java/string/java-string-comparison)
