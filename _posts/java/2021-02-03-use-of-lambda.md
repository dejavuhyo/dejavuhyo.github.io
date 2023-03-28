---
title: 람다의 사용
author: dejavuhyo
date: 2021-02-03 06:15:00 +0900
categories: [Language, Java]
tags: [lambda-use, lambda, 람다-사용, 람다]
---

## 1. 람다의 사용

```java
List<Apple> greenApples = filter(inventory, (Apple a) -> GREEN.equals(a.getColor()));
```

람다는 함수형 인터페이스라는 문맥에서 람다 표현식을 사용할 수 있다. 위 예제에서는 함수형 인터페이스 `Predicate<T>` 를 기대하는 filter 메서드의 두 번째 인수로 람다 표현식을 전달했다.

## 2. 함수형 인터페이스
`Predicate<T>` 인터페이스로 필터 메서드를 파라미터화할 수 있었다. 바로 `Predicate<T>` 가 함수형 인터페이스다. `Predicate<T>` 는 오직 하나의 추상 메서드만 지정한다.

```java
public interface Predicate<T> {
    boolean test (T t);
}
```

간단히 말해 함수형 인터페이스는 정확히 하나의 추상 메서드를 지정하는 인터페이스다. 자바 API의 함수형 인터페이스로 Comparator, Runnalbe 등이 있다.

```java
public interface Comparator<T> { // java.util.Comparator
    int compare(T o1, T o2);
}

public interface Runnable { // java.lang.Runnable
    void run();
}

public interface ActionListener extends EventListener { // java.awt.event.ActionListener
    void actionPerformed(ActionEvent e);
}

public interface Callable<V> { // java.util.concurrent.Callable
    V call() throws Exception;
}

public interface PrivilegedAction<T> { // java.security.PrivilegedAction
    T run();
}
```

> 인터페이스는 디폴드 메서드(인터페이스의 메서드를 구현하지 않은 클래스를 고려해서 기본 구현을 제공하는 바디를 포함하는 메서드)를 포함할 수 있다. 많은 디폴드 메서드가 있더라도 추상 메서드가 오직 하나면 함수형 인터페이스다.

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로 전체 표현식을 함수형 인터페이스의 인스턴스로 취급(기술적으로 따지면 함수형 인터페이스를 구현한 클래스의 인스턴스)할 수 있다. 함수형 인터페이스보다는 덜 깔끔하지만 익명 내부 클래스로도 같은 기능을 구현할 수 있다.

다음 예제는 Runnable이 오직 하나의 추상 메서드 run을 정의하는 함수형 인터페이스이므로 올바른 코드다.

```java
Runnable r1 = () -> System.out.println("Hello Wrold 1"); // 람다 사용

Runnable r2 = new Runnable() { // 익명 클래스 사용
    public void run() {
        System.out.println("Hello World 2");
    }
};

public static void process(Runnable r) {
    r.run();
}
process(r1); // 'Hello Wrold 1' 출력
process(r2); // 'Hello Wrold 2' 출력
process(() -> System.out.println("Hello World 3")); // 직접 전달된 람다 표현식으로 'Hello Wrold 3' 출력
```

## 3. 함수 스크립터
함수형 인터페이스의 추상 메서드 시그니처(signature)는 람다 표현식의 시그이처를 가리킨다. 람다 표현식의 시그니처를 서술하는 메서들를 함수 디스크립터(function descripter)라고 부른다. 예를 들어 Runnable 인터페이스의 유일한 추상 메서드 run은 인수와 반환값이 없으므로(void 반환) Runnable 인터페이스는 인수와 반환값이 없는 시그니처로 생각할 수 있다.

람다와 함수형 인터페이스를 가리키는 특별한 표기법을 사용한다. () -> void 표기는 파라미터 리스트가 없으며 void를 반환하는 함수를 의미한다. 즉 Runnable이 이에 해당한다. (Appel, Apple) -> int는 두 개의 Apple을 인수로 받아 int를 반환하는 함수를 가리킨다.

람다 표현식은 변수에 할당하거나 함수형 인터페이스를 인수로 받는 메서드로 전달할 수 있으며, 함수형 인터페이스의 추상 메서드와 같은 시그니처를 갖는다. 예를 들어 이전에는 process 메서드에 직접 람다 표현식을 전달했다.

```java
public void process(Runnable r) {
    r.run();
}

process(() -> System.out.println("This is awesome!!"));
```

위 코드를 실행하면 'This is awsome!!'이 출력된다. () -> System.out.println("This is awesome!!")은 인수가 없으며 void를 반환하는 람다 표현식이다. 이는 Runnable 인터페이스의 run 메서드 시그니처와 같다.

### 1) 람다와 메서드 호출

조금 이상해 보일 수 있지만 아래는 정상적인 람다 표현식이다.

```java
process(() -> System.out.println("This is awesome"));
```

위 코드에서는 중괄호를 사용하지 않았고 System.out.println은 void를 반환하므로 완벽한 표현식이 아닌 것처럼 보인다.

```java
process(() -> { System.out.println("This is awesome"); });
```

결론적으로 중괄호는 필요 없다. 자바 언어 명세서에는 void를 반환하는 메소드 호출과 관련한 특별한 규직을 정하고 있기 때문이다. 즉, 한 개의 void 메소드 호출은 중괄호로 감쌀 필요가 없다.

> '왜 함수형 인터페이스를 인수로 받는 메서드에만 람다 표현식을 사용할 수 있을까?'라는 의문이 생길 수 있다. 언어 설계자들은 자바에 함수 형식(람다 표현식을 표현하는데 사용한 시그니처와 같은 특별한 표기법)을 추가하는 방법도 대안으로 고려했다. 하지만 언어 설계자들은 언어를 더 복잡하게 만들지 않는 현재 방법을 선택했다. 또한 대부분의 자바 프로그래머가 하나의 추상 메서드를 갖는 인터페이스(예를 들면 이벤트 처리 인터페이스)에 이미 익숙하다는 점도 고려했다.

### 2) @FunctionalInterface는 무엇인가
새로운 자바 API를 살펴보면 함수형 인터페이스 @FunctionalInterface 어노테이션이 추가되어 있다. @FunctionalInterface는 함수형 인터페이스임을 가리키는 어노테이션이다. @FunctionalInterface로 인터페이스를 선언했지만 실제로 함수형 인터페이스가 아니면 컴파일러가 에러를 발생시킨다.

예를 들어 추상 메서드가 한 개 이상이라면 "Multiple nonoverriding abstract methods found in interface Foo(인터페이스 Foo에 오버라이드 하지 않은 여러 추상 메서드가 있음)"같은 에러가 발생할 수 있다.

## [출처 및 참고]
* Modern Java in Action(모던 자바 인 액션)
