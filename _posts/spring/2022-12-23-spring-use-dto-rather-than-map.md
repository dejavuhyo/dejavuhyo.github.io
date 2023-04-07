---
title: Spring Map보다 DTO를 사용해야 하는 이유
author: dejavuhyo
date: 2022-12-23 19:00:00 +0900
categories: [Framework, Spring]
tags: [spring-dto, spring-map, dto-vs-map, spring-dto-map, dto-map, dto-사용, map-사용, dto-가독성, map-가독성, 타입-캐스팅]
---

spring-use-dto-rather-than-map
날짜확인, tags 확인

## 1. 컴파일 에러를 유발할 수 없음
Map의 Value는 Object 타입이다. 그리고 Object 클래스는 최상위 클래스이기 때문에 어떠한 데이터도 받아드릴 수 있다.

Object를 사용할 때의 문제는 어떠한 데이터도 받아드릴 수 있기 때문에 타입 체크를 할 수 없다는 것이다.

만약 long 타입을 넣어줘야 하는데, int 타입을 넣어줘도 해당 코드는 컴파일 에러를 유발하지 않는다.

* 예시

```java
Map<String, Object> map = new HashMap<>();

// long이 아닌 int를 넣어도 컴파일 에러가 발생하지 않음
map.put("height", 180);
```

## 2. String 텍스트를 Key로 사용함
실수로 Map에 데이터를 추가하는 경우 name 대신 namez로 오타가 입력되었다고 가정한다. 만약 이를 인지하지 못한다면 불필요한 문제 때문에 시간 낭비가 발생할 수 있다.

* 예시

```java
Map<String, Object> map = new HashMap<>();
map.put("name", "MangKyu");
String name = (String) map.get("namez");
```

## 3. 가독성이 떨어짐
Map을 사용하는 구조는 가독성이 떨어진다. 어떠한 데이터를 가졌는지 확인할 때는 Map보다 DTO를 확인하는 것이 직관적이고 좋다.

`Map`은 Key 값과 Value 값이 무엇이며 어떠한 타입인지를 파악하기 어렵다. 만약 Map 안에 또 다른 Map이 들어있다면 이러한 문제는 더욱 심각해진다.

결국 Map으로 작성된 코드를 이해하기 위해서는 불필요한 코드 리딩 시간이 필요하고 생산성이 떨어지게 된다.

* 예시

```java
Map<String, Object> result = getResult();
Map<String, Object> user = (Map<String, Object>) map.get("user");
```

## 4. 타입 캐스팅 비용이 발생함
Map에 있는 데이터를 꺼내서 사용하기 위해서는 반드시 타입 캐스팅해야 한다.

* 예시

```java
String name = (String) map.get("name");
```

## 5. 불변성을 확보할 수 없음
Map을 사용하면 해당 데이터의 불변성을 확보할 수 없다. 누군가가 put 코드를 추가하였다면 기존의 데이터는 없어지고 잘못된 데이터로 덮어씌워진다.

* 예시

```java
Map<String, Object> map = new HashMap<>();
map.put("name", "GilDong");

// 불변성을 확보할 수 없고 값이 변경될 수 있음
map.put("name", 1);
```

## [출처 및 참고]
* [https://mangkyu.tistory.com/164](https://mangkyu.tistory.com/164)
