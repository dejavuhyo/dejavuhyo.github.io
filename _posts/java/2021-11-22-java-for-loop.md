---
title: Java For Loop
author: dejavuhyo
date: 2021-11-22 09:30:00 +0900
categories: [Language, Java]
tags: [java-for-loop, for-loop, java-for, java-loop, 자바-for-loop]
---

## 1. 루프
루프는 루프 카운터를 증가시키고 평가하여 특정 연산을 반복할 수 있는 제어 구조이다.

첫 번째 반복 전에 루프 카운터가 초기화된 다음 조건 평가가 수행되고 단계 정의가 수행된다(일반적으로 단순 증가).

for 루프 의 구문은 다음과 같다.

```java
for (initialization; Boolean-expression; step) 
    statement;
```

간단한 예이다.

```java
for (int i = 0; i < 5; i++) {
    System.out.println("Simple for loop: i = " + i);
}
```

initialization, Boolean 표현, 그리고 step에 사용 하기위한 문은 선택 사항이다. 다음은 무한 for 루프의 예이다.

```java
for ( ; ; ) {
    // Infinite for loop
}
```

### 1) 루프 레이블 지정
for 루프에 레이블을 지정할 수도 있다. 특정 for 루프에서 중단/계속할 수 있도록 for 루프가 중첩되어 있으면 유용하다.

```java
aa: for (int i = 1; i <= 3; i++) {
    if (i == 1)
      continue;
    bb: for (int j = 1; j <= 3; j++) {
        if (i == 2 && j == 2) {
            break aa;
        }
        System.out.println(i + " " + j);
    }
}
```

## 2. for 루프 개선
Java 5 이후로, 우리는 enhanced라고 불리는 두 번째 종류의 루프를 가지고 있는데, 이를 위해 배열이나 컬렉션의 모든 요소를 더 쉽게 반복할 수 있다.

향상된 for 루프 구문은 다음과 같다.

```java
for(Type item : items)
    statement;
```

이 루프는 표준 for 루프와 비교하여 단순화되었으므로 루프를 초기화할 때 두 가지만 선언하면 된다.

* 현재 반복 중인 요소에 대한 핸들

* 반복 중인 소스 배열/컬렉션

항목의 각 요소에 대해 항목 변수에 요소를 할당 하고 루프 본문을 실행한다.

간단한 예이다.

```java
int[] intArr = { 0,1,2,3,4 }; 
for (int num : intArr) {
    System.out.println("Enhanced for-each loop: i = " + num);
}
```

이를 사용하여 다양한 Java 데이터 구조를 반복할 수 있다.

```List<String>``` 목록 개체가 주어지면 반복할 수 있다.

```java
for (String item : list) {
    System.out.println(item);
}
```

```Set<String>``` set에 대해서도 유사하게 반복할 수 있다.

```java
for (String item : set) {
    System.out.println(item);
}
```

```Map<String, Integer>``` 맵이 주어지면 이를 반복할 수도 있다.

```java
for (Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(
      "Key: " + entry.getKey() + 
      " - " + 
      "Value: " + entry.getValue());
}
```

### 1) Iterable.forEach()
Java 8부터 for-each 루프를 약간 다른 방식으로 활용할 수 있다. 이제 Iterable 인터페이스에 수행하려는 작업을 나타내는 람다식을 허용 하는 전용 forEach() 메서드가 있다.

내부적으로는 단순히 작업을 표준 루프에 위임한다.

```java
default void forEach(Consumer<? super T> action) {
    Objects.requireNonNull(action);
    for (T t : this) {
        action.accept(t);
    }
}
```

아래는 예이다.

```java
List<String> names = new ArrayList<>();
names.add("Larry");
names.add("Steve");
names.add("James");
names.add("Conan");
names.add("Ellen");

names.forEach(name -> System.out.println(name));
```

## [출처 및 참고]
* <https://www.baeldung.com/java-for-loop>
