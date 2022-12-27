---
title: Java private static final과 private final의 차이
author: dejavuhyo
date: 2022-12-27 21:20:00 +0900
categories: [Language, Java]
tags: [private-static-final, private-final, static, final]
---

## 1. static이란
static은 '정적인', '움직임이 없는'이라는 의미로, static을 사용하면 JVM의 static 메모리에 올라간다. 즉, static 데이터는 프로그램 실행 직후부터 끝날 때까지 메모리 수명이 유지된다.

static 메모리에 올라가기 때문에 초기화 과정 필요 없이 static이 선언된 변수, 메서드에 바로 접근이 가능하다.

static을 사용한다는 의미는 해당 객체를 공유하겠다는 의미이며, 다른 곳에서 해당 객체를 사용한다면 그 객체는 항상 동일한 객체라는 뜻이다.

## 2. final이란
final은 불변하도록 만드는 것이 아니라 재할당할 수 없도록 만드는 것이다. 상속하거나, 최초 초기화 이후 다시 초기화할 수 없다.

## 3. private static final과 private final의 차이

### 1) private static final
`private static final`을 선언한 변수를 사용하면 재할당하지 못하며, 메모리에 한 번 올라가면 같은 값을 클래스 내부의 전체 필드, 메서드에서 공유한다.

### 2) private final
`private final`을 선언한 변수를 사용하면 재할당하지 못하며, 해당 필드, 메서드별로 호출할 때마다 새로이 값이 할당(인스턴스화)한다.

## [출처 및 참고]
* [https://zorba91.tistory.com/275](https://zorba91.tistory.com/275)
* [https://wonyong-jang.github.io/java/2020/03/28/Java-Static-Final.html](https://wonyong-jang.github.io/java/2020/03/28/Java-Static-Final.html)
* [https://devbox.tistory.com/entry/Java-static](https://devbox.tistory.com/entry/Java-static)
