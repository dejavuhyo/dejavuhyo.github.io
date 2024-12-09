---
title: Java List를 Array로 변환
author: dejavuhyo
date: 2024-12-09 15:17:00 +0900
categories: [Language, Java]
tags: [java-list-array, list-array, list, array, 리스트-배열, 리스트, 배열]
---

## 1. toArray()
List를 Array로 변환하려면 `java.util.List`의 `toArray()` 메소드를 사용한다.

* 예제

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

public class Test {
    public static void main(String[] args) {
//        List<Integer> list = IntStream.range(1, 6).boxed().collect(Collectors.toList());
        List<Integer> list = IntStream.range(1, 6).boxed().toList();
        Integer[] ary = list.toArray(Integer[]::new);
        System.out.println(Arrays.toString(ary));
    }
}
```

* 결과

```text
[1, 2, 3, 4, 5]
```

## 2. 기본 타입 배열로 변환
기본 타입(primitive type)으로 변환 후 `toArray()`를 사용해야 한다.

`Stream.map()`은 객체를 다른 객체 타입 변환이 가능하지만 기본형을 지원하진 않고, 기본형에 맞는 별도의 메서드를 제공한다.

`mapToInt()`, `mapToDouble()`, `mapToLong()` 메서드는 기본형 특화 스트림(IntStream, DoubleStream, LongStream)을 반환한다.

* 예제

```java
public class Test {
    public static void main(String[] args) {
        List<Integer> list = IntStream.range(1, 6).boxed().toList();
        int[] ary = list.stream().mapToInt(Integer::intValue).toArray();
        System.out.println(Arrays.toString(ary));
    }
}
```

* 결과

```text
[1, 2, 3, 4, 5]
```

## [출처 및 참고]
* [https://gaeggu.tistory.com/24](https://gaeggu.tistory.com/24)
