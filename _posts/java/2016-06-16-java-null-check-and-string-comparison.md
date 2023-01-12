---
title: Java null 체크 및 스트링 비교
author: dejavuhyo
date: 2016-06-16 10:16:00 +0900
categories: [Language, Java]
tags: [java-null-check, java-string-comparision, null-check, string-comparision, 자바-null-체크, 자바-스트링-비교, null-체크, 스트링-비교]
---

## 1. null 체크
객체의 메소드를 사용할 때 반드시 객체가 null 여부를 먼저 체크해야 한다.

### 1) Bad

```java
if (param.equals("id")) {
}
```

### 2) Good

```java
if (param != null && param.equals("id")) {
}
```

### 3) Good

```java
if ("id".equals(param)) {
}
```

1번의 경우 param 이 null 이면 NullPointException이 발생한다.

NullPointException을 handling하지 않은경우 WAS에서 제대로 처리하지 못해 메모리 누수 및 WAS 장애의 원인이 된다.

param이 null이라면, 2번의 경우 "id"라는 스트링을 생성하지 않으므로 가장 좋다고 할 수 있다. 3번의 경우는 2번 대신 코딩 편의를 위해서 쓴다고 할 수 있다.

자바에서 가장 흔하게 사용되는 객체인 String을 예로 들었지만, 다른 객체들도 마찬가지 이다. NullPointException을 없애고, 불필요한 객체 생성을 줄이면 WAS의 메모리 사용량과 메모리 누수를 상당부분 줄일 수 있다.

## 2. 스트링 비교
자바에서 String에 값을 체크하기 위해 아래과 같이 체크하는 경우가 있다.

```java
if (param != null && param.equals("")) {
// 값이 있는 경우 처리
} else { 
// 값이 없는 경우 처리
}
```

이 경우 다음과 같이 사용하는 것이 좋다.

```java
if (param != null && param.length() != 0) {
// 값이 있는 경우 처리
} else { 
// 값이 없는 경우 처리
}
```

또는

```java
if (param == null || param.length() == 0) {
// 값이 없는 경우 처리
} else { 
// 값이 있는 경우 처리
}
```

`equals("")`를 사용하면 불필요하게 새로운 스트링인 `""`를 생성하게 되고, 함수 내부에서 몇 번의 비교후에 다른 스트링으로 형변환 및 치환도 한다.

반면, `length()` 함수는 내장객체인 int 변수 하나만 리턴하기 때문에 빠르다.

## [출처 및 참고]
* [http://tazz.tistory.com/30](http://tazz.tistory.com/30)
