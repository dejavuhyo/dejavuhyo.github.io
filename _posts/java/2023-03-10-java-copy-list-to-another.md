---
title: Java 다른 List에 List 복사
author: dejavuhyo
date: 2023-03-10 10:20:00 +0900
categories: [Language, Java]
tags: [java-copy-list, copy-list, java-list, 자바-리스트-복사, 리스트-복사, 자바-리스트]
---

## 1. Constructor
목록을 복사하는 간단한 방법은 컬렉션을 인수로 사용하는 생성자를 사용하는 것이다.

```java
List<Plant> copy = new ArrayList<>(list);
```

여기에서 참조를 복사하고 개체를 복제하지 않기 때문에 한 요소에서 수행된 모든 수정은 두 목록 모두에 영향을 미친다.

따라서 불변 객체를 복사하기 위해 생성자를 사용하는 것이 좋다.

```java
List<Integer> copy = new ArrayList<>(list);
```

정수는 변경할 수 없는 클래스이다. 해당 값은 인스턴스가 생성될 때 설정되며 절대 변경할 수 없다.

따라서 Integer 참조는 여러 목록과 스레드에서 공유할 수 있으며 아무도 해당 값을 변경할 수 없다.

## 2. List ConcurrentAccessException
List로 작업하는 일반적인 문제는 `ConcurrentAccessException`이다. 이는 일반적으로 다른 스레드에서 목록을 복사하려고 시도하는 동안 목록을 수정하고 있음을 의미한다.

이 문제를 해결하려면 다음 중 하나를 수행해야 한다.

* 동시 액세스용으로 설계된 컬렉션 사용

* 컬렉션을 반복하도록 적절하게 잠금

* 원본 컬렉션을 복사할 필요가 없도록 하는 방법 찾기

마지막 접근 방식을 고려하면 스레드로부터 안전하지 않다. 첫 번째 옵션으로 문제를 해결하려면 [CopyOnWriteArrayList](https://www.baeldung.com/java-copy-on-write-arraylist)를 사용할 수 있다. 여기서 기본 배열의 새 복사본을 만들어 모든 가변 작업을 구현한다.

`Collection`을 잠그려면 `ReentrantReadWriteLock`과 같은 직렬화된 읽기/쓰기 액세스에 잠금 프리미티브를 사용할 수 있다.

## 3. AddAll
요소를 복사하는 또 다른 접근 방식은 addAll 메서드를 사용하는 것이다.

```java
List<Integer> copy = new ArrayList<>();
copy.addAll(list);
```

이 메서드를 사용할 때마다 생성자와 마찬가지로 두 목록의 내용이 동일한 개체를 참조한다는 점을 염두에 두는 것이 중요하다.

## 4. Collections.copy
Collections 클래스는 컬렉션에서 작동하거나 컬렉션을 반환 하는 정적 메서드로만 구성된다.

그중 하나는 최소한 소스만큼 긴 소스 목록과 대상 목록이 필요한 copy이다.

원본과 같이 대상 목록에서 복사된 각 요소의 인덱스를 유지한다.

```java
List<Integer> source = Arrays.asList(1,2,3);
List<Integer> dest = Arrays.asList(4,5,6);
Collections.copy(dest, source);
```

위의 예에서 두 목록의 크기가 같기 때문에 dest 목록의 모든 이전 요소를 덮어쓴다.

대상 목록 크기가 소스보다 큰 경우

```java
List<Integer> source = Arrays.asList(1, 2, 3);
List<Integer> dest = Arrays.asList(5, 6, 7, 8, 9, 10);
Collections.copy(dest, source);
```

여기에서는 처음 세 항목만 덮어쓰고 목록의 나머지 요소는 보존했다.

## 5. Java 8 사용
이 버전의 Java는 새로운 도구를 추가하여 가능성을 확장합니다. 다음 예제는 Stream이다.

```java
List<String> copy = list.stream()
  .collect(Collectors.toList());
```

이 옵션의 주요 장점은 건너뛰기와 필터를 사용할 수 있다는 것이다. 다음 예는 첫 번째 요소를 건너뛴다.

```java
List<String> copy = list.stream()
  .skip(1)
  .collect(Collectors.toList());
```

문자열의 길이로 필터링하거나 객체의 속성을 비교하여 필터링하는 것도 가능하다.

```java
List<String> copy = list.stream()
  .filter(s -> s.length() > 10)
  .collect(Collectors.toList());
```

```java
List<Flower> flowers = list.stream()
  .filter(f -> f.getPetals() > 6)
  .collect(Collectors.toList());
```

`null-safe` 방식으로 작업하고 싶을 가능성이 있다.

```java
List<Flower> flowers = Optional.ofNullable(list)
  .map(List::stream)
  .orElseGet(Stream::empty)
  .collect(Collectors.toList());
```

다음과 같은 방식으로도 요소를 건너뛰고 싶을 것이다.

```java
List<Flower> flowers = Optional.ofNullable(list)
  .map(List::stream).orElseGet(Stream::empty)
  .skip(1)
  .collect(Collectors.toList());
```

## 6. Java 10 사용
주어진 Collection의 요소를 포함하는 불변 목록을 만들 수 있다.

```java
List<T> copy = List.copyOf(list);
```

유일한 조건은 지정된 Collection이 null이거나 null 요소를 포함하지 않아야 한다는 것이다.

## [출처 및 참고]
* [https://www.baeldung.com/java-copy-list-to-another](https://www.baeldung.com/java-copy-list-to-another)
