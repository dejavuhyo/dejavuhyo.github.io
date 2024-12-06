---
title: Java Array를 List로 변환
author: dejavuhyo
date: 2024-12-06 10:15:00 +0900
categories: [Language, Java]
tags: [java-array-list, array-list, array, list, 배열-리스트, 배열, 리스트]
---

## 1. 객체타입 Array

### 1) Arrays.asList()

* 예제

```java
import java.util.Arrays;
import java.util.List;

public class Test {
    public static void main(String[] args) {
        Integer[] ary = {1, 2, 3, 4, 5};
        List<Integer> list = Arrays.asList(ary);
        System.out.println(list);
    }
}
```

* 결과

```text
[1, 2, 3, 4, 5]
```

### 2) List.of()
`List.of()`는 Java 9부터 지원한다.

* 예제

```java
import java.util.List;

public class Test {
    public static void main(String[] args) {
        Integer[] ary = {1, 2, 3, 4, 5};
        List<Integer> list = List.of(ary);
        System.out.println(list);
    }
}
```

* 결과

```text
[1, 2, 3, 4, 5]
```

### 3) new ArrayList<>(Arrays.asList())
`Arrays.asList()` 및 `List.of()`는 고정 길이의 List를 리턴하므로 추가 및 삭제가 불가능하다.

`java.util.ArrayList`와 다른 List 구현 클래스를 반환하기 때문이다.

* `Arrays.asList()` → `java.util.Arrays.ArrayList`

* `List.of()` → `java.util.ImmutableCollections.ListN`

* 예제

```java
import java.util.List;

public class Test {
    public static void main(String[] args) {
        Integer[] ary = {1, 2, 3, 4, 5};
        List<Integer> list = List.of(ary);
        list.add(6);
        System.out.println(list);
    }
}
```

* 결과

```text
Exception in thread "main" java.lang.UnsupportedOperationException
	at java.base/java.util.ImmutableCollections.uoe(ImmutableCollections.java:142)
	at java.base/java.util.ImmutableCollections$AbstractImmutableCollection.add(ImmutableCollections.java:147)
```

원본 Array의 값과 리턴된 List의 값이 동기화 되는 것을 막기 위해 반환된 List를 변경하려면, **새로운 ArrayList 객체를 생성**해서 변경할 수 있다.

* 예제

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Test {
    public static void main(String[] args) {
        Integer[] ary = {1, 2, 3, 4, 5};
        List<Integer> list = new ArrayList<>(Arrays.asList(ary));
        list.add(6);
        System.out.println(list);
    }
}
```

* 결과

```text
[1, 2, 3, 4, 5, 6]
```

### 4) Collectors.toList()
Stream을 이용하여 Array를 List로 변환할 수 있다.

* 예제

```java
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class Test {
    public static void main(String[] args) {
        Integer[] ary = {1, 2, 3, 4, 5};
        List<Integer> list = Stream.of(ary).collect(Collectors.toList());
        list.add(6);
        System.out.println(list);
    }
}
```

* 결과

```text
[1, 2, 3, 4, 5, 6]
```

## 2. 기본 타입 Array
리스트는 기본 타입(primitive type)을 지원하지 않기 때문에 `boxed()`를 이용해 참조 타입(reference type)으로 박싱한 후 리스트로 변환한다.

* 예제

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class Test {
    public static void main(String[] args) {
        int[] ary = {1, 2, 3, 4, 5};
        List<Integer> list = Arrays.stream(ary).boxed().collect(Collectors.toList());
        list.add(6);
        System.out.println(list);
    }
}
```

* 결과

```text
[1, 2, 3, 4, 5, 6]
```

## [출처 및 참고]
* [https://hianna.tistory.com/551](https://hianna.tistory.com/551)
* [https://gaeggu.tistory.com/24](https://gaeggu.tistory.com/24)
