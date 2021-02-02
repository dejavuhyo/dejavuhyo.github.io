---
title: 람다 표현식
author: dejavuhyo
date: 2021-01-29 13:45:00 +0900
categories: [Language, Java]
tags: [lambda-expressions, lambda, 람다-표현식, 람다]
---

## 1. 람다란
람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이라고 할 수 있다. 람다 표현식에는 이름은 없지만, 파라미터 리스트, 바디, 반환 방식, 발생할 수 있는 예외 리스트는 가질 수 있다. 람다의 특징은 아래와 같다.

* 익명
  - 보통의 메서드와 달리 이름이 없으므로 익명이라 표현한다. 구현해야 할 코드에 대한 걱정거리가 줄어든다.

* 함수
  - 람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다. 하지만 메서드처럼 파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트를 포함한다.

* 전달
  - 람다 표현식을 메스드 인수로 전달하거나 변수로 저장할 수 있다.

* 간결성
  - 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없다.

람다라는 용어는 람다 미적분학 하계에서 개발한 시스템에서 유래했다. 람다 표현식이 중요한 이유는 코드를 전달하는 과정에서 자질구레한 코드가 많이 생긴다. 다행히 람다로 이 문제를 해결할 수 있다. 즉, 람다를 이용해서 간결한 방식으로 코드를 전달할 수 있다.

람다가 기술적으로 자바 8 이전의 자바로 할 수 없었던 일을 제공하는 것은 아니다. 다만 동작 파라미터를 이용할 때 익명 클래스 등 판에 박힌 코스를 구현할 필요가 없다. 람다 표현식을 이용하면 동작 파라미터 형식의 코드를 더 쉽게 구현할 수 있다. 결과적으로 코드가 간결해지고 유연해진다.

* 기존 코드

```java
Comparator<Apple> byWeight = new Comparator<Apple>() {
	public int compare(Apple a1, Apple a2) {
		return a1.getWeight().compareTo(a2.getWeight());
	}
};
}
```

* 람다를 이용한 코드

```java
Comparator<Apple> byWeight = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

코드가 훨씬 간단해졌다. 람다 표현식을 이용하면 compare 메서드의 바디를 직접 전달하는 것처럼 코드를 전달할 수 있다.

* 람다의 표현식은 파라미터, 화살표, 바디로 이루어진다.

| 람다 파라미터 | 화살표 | 람다 바디 |
|:---:|:---:|:---:|
| (Apple a1, Apple a2) | -> | a1.getWeight().compareTo(a2.getWeight()); |

* 파라미터 리스트
  - Comparator의 compare 메서드 파라미터(사과 두 개)

* 화살표
  - 화살표(->)는 람다의 파라미터 리스트와 바디를 구분한다.

* 람다 바디
  - 두 사과의 무게를 비교한다. 람다의 반환값에 해당하는 표현식이다.

다음은 자바 8에서 지원하는 다섯 가지 람다 표현식 예제다.

* 자바 8의 유요한 람다 표현식

```java
(String s) -> s.length() // String 형식의 파라미터 하나를 가지며 int를 반환한다.람다 표현식에는 return이 함축되어 있으므로 return 문을 명시적으로 사용하지 않아도 된다.
(Apple a) -> a.getWeight() > 150 // Apple 형식의 파라미터 하나를 가지며 boolean(사과가 150그램 보다 무거운지 결정)을 반환한다.
(int x, int y) -> {
	System.out.println("Result:");
	System.out.println(x + y); // int 형식의 파라미터 두 개를 가지며 리턴값이 없다(void 리턴). 이 예제에서 볼 수 있듯이 람다 표현식은 어러 행의 문장을 포함할 수 있다.
}
() -> 42 // 파라미터가 없으면 int 42를 반환한다.
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()) // Apple 형식의 파라미터 두 개를 가지며 int(두 사과의 무게 비교 결과)를 반환한다.
```

자바 설계자능 이미 C#이나 스칼라 같은 비슷한 기능을 가진 다른 언어와 비슷한 문법을 자바에 적용하기로 했다. 다음은 표현식 스타일(expression style) 람다라고 알려진 람다의 기본 문법이다.

```text
(parameters) -> expression
```

또는 블록 스타일(block style)로 표현할 수 있다.

```text
(parameters) -> { statements; }
```

람다 표현식의 문법은 단순하다.

* 람다 예제

| 사용 사례 | 람다 예제 |
|:---:|:---:|
| 불리언 표현식 |  ```(List<String>) -> list.isEmpty()``` |
| 객체 생성 | ```() -> new Apple(10)``` |
| 객체에서 소비 | ```(Apple a) -> { System.out.println(a.getWeight());``` |
| 객체에서 선택/추출 | ```(String s) -> s.length()``` |
| 두 값을 조합 | ```(int a, int b) -> a * b``` |
| 두 객체 비교 | ```(Apple a1, Apple a2) -> a.getWeight().compareTo(a2.getWeight())``` |

## [출처 및 참고]
* Modern Java in Action(모던 자바 인 액션)
