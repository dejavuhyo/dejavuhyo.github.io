---
title: Java 메인 메서드
author: dejavuhyo
date: 2021-11-18 09:30:00 +0900
categories: [Language, Java]
tags: [java-main-method, main-method, 자바-메인-메서드, 메인-메서드]
---

## 1. 공통 서명
가장 일반적인 기본 메서드 템플릿은 다음과 같다.

```java
public static void main(String[] args) { }
```

그것이 우리가 배운 방식이며 IDE가 코드를 자동 완성하는 방식이다. 그러나 이것이 이 방법이 가정할 수 있는 유일한 형식은 아니다. 사용할 수 있는 몇 가지 유효한 변형이 있으며 모든 개발자가 이 사실에 주의를 기울이는 것은 아니다.

이러한 메서드 서명에 대해 알아보기 전에 공통 서명의 각 키워드 의미를 검토한다.

* __public:__ 전역 가시성을 의미하는 액세스 수정자

* __static:__ 메서드는 클래스에서 직접 액세스할 수 있다. 참조를 갖고 사용하기 위해 객체를 인스턴스화할 필요가 없다.

* __void:__ 이 메서드가 값을 반환하지 않음을 의미한다.

* __main:__ 메소드의 이름으로 JVM이 Java 프로그램을 실행할 때 찾는 식별자이다.

args 파라미터는, 이 값은 메서드에서 받은 값을 나타낸다. 이것은 우리가 프로그램을 처음 시작할 때 프로그램에 인수를 전달하는 방법이다.

매개 변수 arg는 문자열의 배열이다. 다음과 같다.

```java
java CommonMainMethodSignature foo bar
```

CommonMainMethodSignature라는 Java 프로그램을 실행하고 2개의 인수(foo 및 bar)를 전달한다. 이러한 값은 기본 메서드 내부에서 args[0] (값으로 foo 사용) 및 args[1] (값으로 막대 사용)로 액세스할 수 있다.

다음 예에서는 테스트 또는 프로덕션 매개변수를 로드할지 여부를 결정하기 위해 args를 확인한다.

```java
public static void main(String[] args) {
    if (args.length > 0) {
        if (args[0].equals("test")) {
            // load test parameters
        } else if (args[0].equals("production")) {
            // load production parameters
        }
    }
}
```

IDE가 프로그램에 인수를 전달할 수도 있음을 항상 기억하는 것이 좋다.

## 2. main() 메서드를 작성하는 다양한 방법
main 메소드를 작성하는 몇 가지 다른 방법을 확인한다. 흔하지는 않지만 유효한 서명이다.

이들 중 어느 것도 기본 메소드에 국한되지 않으며 모든 Java 메소드와 함께 사용할 수 있지만 기본 메소드 의 유효한 부분이기도 한다.

대괄호는 일반 템플릿에서와 같이 String 근처에 배치 하거나 양쪽의 args 근처에 배치할 수 있다.

```java
public static void main(String []args) { }
```

```java
public static void main(String args[]) { }
```

인수는 varargs로 나타낼 수 있다.

```java
public static void main(String...args) { }
```

부동 소수점 값으로 작업할 때 프로세서 간의 호환성을 위해 사용되는 main() 메서드에 strictfp를 추가할 수도 있다.

```java
public strictfp static void main(String[] args) { }
```

synchronized 및 final 또한 기본 메서드에 대한 유효한 키워드 이지만 여기에서는 영향을 미치지 않는다.

반면에 배열이 수정되는 것을 방지하기 위해 args에 final을 적용할 수 있다.

```java
public static void main(final String[] args) { }
```

이러한 예제를 끝내기 위해 위의 키워드를 모두 사용하여 주요 메소드를 작성할 수도 있다(물론 실제 응용 프로그램에서는 사용하지 않을 수도 있다).

```java
final static synchronized strictfp void main(final String[] args) { }
```

## 3. 하나 이상의 main() 메서드 사용
애플리케이션 내에서 둘 이상의 기본 메서드를 정의할 수도 있다.

사실, 어떤 사람들은 개별 클래스를 검증하기 위한 원시 테스트 기술로 이것을 사용한다(JUnit과 같은 테스트 프레임워크가 이 활동에 대해 더 많이 표시 되지만).

JVM이 애플리케이션의 진입점으로 실행해야 하는 기본 메소드를 지정하기 위해 MANIFEST.MF 파일을 사용 한다. 매니페스트 내에서 기본 클래스를 나타낼 수 있다.

```java
Main-Class: mypackage.ClassWithMainMethod
```

이것은 실행 가능한 `.jar` 파일을 생성할 때 주로 사용된다. `META-INF/MANIFEST.MF` (UTF-8로 인코딩됨)에 있는 매니페스트 파일을 통해 실행을 시작하는 기본 메서드가 있는 클래스를 나타낸다.

## [출처 및 참고]
* <https://www.baeldung.com/java-main-method>
