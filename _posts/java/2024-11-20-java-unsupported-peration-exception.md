---
title: Java java.lang.UnsupportedOperationException 오류
author: dejavuhyo
date: 2024-11-21 14:30:00 +0900
categories: [Language, Java]
tags: [java-error, unsupported-peration-exception, 자바-오류]
---

## 1. UnsupportedOperationException 에러

* 에러 예제

```java
public static void main(String[] args) {
    int[] intAry = {1, 2, 3, 4, 5};
    Integer[] integerAry = {1, 2, 3, 4, 5};
    String[] stringAry = {"aa", "bb", "cc", "dd", "ee"};

    List<Integer> intList = Arrays.stream(intAry).boxed().toList();
    intList.add(6);
    System.out.println("intList = " + intList);

    List<Integer> integerList = Arrays.stream(integerAry).toList();
    integerList.add(6);
    System.out.println("integerList = " + integerList);

    List<String> strList = Arrays.asList(stringAry);
    strList.add("ff");
    Collections.reverse(strList);
    System.out.println("strList = " + strList);
}
```

* 에러 메시지

```text
Exception in thread "main" java.lang.UnsupportedOperationException
	at java.base/java.util.ImmutableCollections.uoe(ImmutableCollections.java:142)
	at java.base/java.util.ImmutableCollections$AbstractImmutableList.set(ImmutableCollections.java:260)
	at java.base/java.util.Collections.swap(Collections.java:501)
	at java.base/java.util.Collections.reverse(Collections.java:382)
	at com.example.demo.Test.main(Test.java:19)
```

## 2. 원인
`Arrays.asList()` 메소드는 고정길이의 원배열의 list view를 리턴 한다.

`Arrays.asList()`를 사용해서 배열을 List로 변환한 후, 길이를 List에 값을 추가하는 것이 불가능하고, 원래 배열의 값을 변경하면 List의 값도 같이 변경된다.

원본 배열의 값과 변환 된 List의 값이 동기화 되는 것을 막기 위해 `Arrays.asList()`로 변환한 List를 새로운 ArrayList 객체를 생성해서 사용할 수 있다.

## 3. 해결 방법

* 예제

```java
public static void main(String[] args) {
    int[] intAry = {1, 2, 3, 4, 5};
    Integer[] integerAry = {1, 2, 3, 4, 5};
    String[] stringAry = {"aa", "bb", "cc", "dd", "ee"};

//        List<Integer> intList = Arrays.stream(intAry).boxed().collect(Collectors.toList());
    List<Integer> intList = new ArrayList<>(Arrays.stream(intAry).boxed().toList());
    intList.add(6);
    System.out.println("intList = " + intList);

    List<Integer> integerList = new ArrayList<>(Arrays.asList(integerAry));
    integerList.add(6);
    System.out.println("integerList = " + integerList);

    List<String> strList = new ArrayList<>(Arrays.asList(stringAry));
    strList.add("ff");
    Collections.reverse(strList);
    System.out.println("strList = " + strList);
}
```

## [출처 및 참고]
* [https://greed-yb.tistory.com/146](https://greed-yb.tistory.com/146)
* [https://cordcat.tistory.com/58](https://cordcat.tistory.com/58)
