---
title: Java 스트림 마지막 Element 얻기
author: dejavuhyo
date: 2024-05-14 15:17:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-element, 자바-스트림]
---

## 1. Reduce API 사용
Reduce는 Stream의 요소 집합을 단일 요소로 줄인다.

이 경우 스트림의 마지막 요소를 가져오기 위해 요소 집합을 줄인다. 이 방법은 순차 스트림에 대한 결정론적 결과만 반환한다.

List String을 사용 하고 List에서 스트림을 가져온 다음 다음을 줄인다.

```java
List<String> valueList = new ArrayList<>();
valueList.add("Joe");
valueList.add("John");
valueList.add("Sean");

Stream<String> stream = valueList.stream();
stream.reduce((first, second) -> second)
  .orElse(null);
```

여기서 스트림은 마지막 요소만 남게 되는 수준으로 축소된다. 스트림이 비어 있으면 null 값을 반환한다.

## 2. 건너뛰기 기능 사용
스트림의 마지막 element를 가져오는 다른 방법은 그 앞의 모든 요소를 ​​건너뛰는 것 이다. 이는 Stream 클래스의 Skip 기능을 사용하여 달성할 수 있다. 이 경우 Stream을 두 번 소비하므로 성능에 어느 정도 영향이 있다.

문자열 값 목록을 만들고 해당 크기 함수를 사용하여 마지막 element에 도달하기 위해 건너뛸 element 수를 결정한다.

다음은 Skip을 사용하여 마지막 element를 가져오는 예제 코드이다.

```java
List<String> valueList = new ArrayList<String>();
valueList.add("Joe");
valueList.add("John");
valueList.add("Sean");

long count = valueList.stream().count();
Stream<String> stream = valueList.stream();
   
stream.skip(count - 1).findFirst().get();
```

"Sean"은 결국 마지막 element가 된다.

## 3. Infinite Stream 마지막 Element 얻기
무한 스트림의 마지막 element를 얻으려고 하면 무한 element에 대해 무한한 평가 시퀀스가 ​​수행된다. 제한 작업을 사용하여 무한 스트림을 특정 수의 element로 제한하지 않으면 건너뛰기와 축소 모두 평가 실행에서 돌아오지 않는다.

다음은 무한 스트림을 가져와 마지막 element를 가져오려고 시도한 예제 코드이다.

```java
Stream<Integer> stream = Stream.iterate(0, i -> i + 1);
stream.reduce((first, second) -> second).orElse(null);
```

결과적으로 스트림은 평가에서 돌아오지 않으며 결국 프로그램 실행이 중단 된다.

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-last-element](https://www.baeldung.com/java-stream-last-element)
