---
title: Apache Commons Collections CircularFifoQueue
author: dejavuhyo
date: 2023-07-06 09:30:00 +0900
categories: [Language, Java]
tags: [apache-commons, apache-commons-circularfifoqueue, circularfifoqueue, 아파치-커먼즈]
---

## 1. 메이븐 종속성
Maven 프로젝트의 경우 필요한 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.1</version>
</dependency>
```

## 2. 생성자
CircularFifoQueue 객체를 생성하기 위해 기본 크기가 32인 대기열을 생성하는 기본 생성자를 사용할 수 있다.

```java
CircularFifoQueue<String> bits = new CircularFifoQueue();
```

원하는 최대 대기열 크기를 알고 있으면 int를 인수로 사용하여 크기를 지정하는 생성자를 사용할 수 있다.

```java
CircularFifoQueue<String> colors = new CircularFifoQueue<>(5);
```

생성자에 컬렉션을 인수로 제공하여 CircularFifoQueue 객체를 생성하는 옵션도 있다.

이 경우 큐는 컬렉션의 요소로 채워지며 큐의 크기는 컬렉션의 크기와 동일하다.

```java
CircularFifoQueue<String> daysOfWeek = new CircularFifoQueue<>(days);
```

> __참고__: 이 대기열은 구성될 때 이미 가득 차 있기 때문에 추가하면 처음 생성된 요소가 삭제된다.

## 3. 요소 추가
모든 Queue 구현과 마찬가지로 add 및 offer 메서드를 사용하여 요소를 추가할 수 있다. Queue JavaDoc은 용량이 제한된 대기열과 함께 작동할때 제안 방법이 사용되도록 지정 한다.

그러나 CircularFifoQueue는 비차단이므로 삽입이 실패할 수 없다. 결과적으로 add 및 offer 메서드는 동일한 동작을 나타낸다.

add 메서드를 사용하여 색상 대기열에 요소를 추가하는 방법이다.

```java
colors.add("Red");
colors.add("Blue");
colors.add("Green");
```

제안 방법을 사용하여 몇 가지 요소를 추가한다.

```java
colors.offer("White");
colors.offer("Black");
```

## 4. 요소 제거 및 검색
CircularFifoQueue 클래스는 대기열의 요소를 조작해야 할 때 유용할 수 있는 몇 가지 방법을 제공한다. 일부 메서드는 대기열에서 요소를 가져오는 데 사용되며, 일부는 요소를 제거하는 데 사용되며, 일부는 두 작업을 동시에 수행하는 데 사용된다.

### 1) Peek Method
peek 메서드는 비파괴적이며 대기열의 헤드를 반환 한다.

이 메서드는 호출 사이에 대기열의 요소에 변경 사항이 없는 한 항상 동일한 요소를 반환한다. 대기열이 비어 있으면 peek는 null을 반환한다.

```java
String colorsHead = colors.peek();
```

### 2) Element Method
element 메서드는 peek와 비슷하다. 큐의 현재 헤드를 반환 한다.

그러나 element 메서드는 대기열이 비어 있는 경우 예외를 발생시킨다.

```java
colorsHead = colors.element();
```

### 3) Get Method
큐에서 특정 요소를 가져와야 하는 경우 get 메서드를 사용할 수 있다. 이 메서드는 원하는 요소의 인덱스를 인수로 사용한다. 대기열의 인덱스는 0부터 시작한다.

이전에 요소로 채운 색상 대기열에서 요소를 가져온다.

```java
String color = colors.get(1);
```

"Blue"가 반환된다.

이제 세 개의 요소를 대기열에 추가하고 이 결과를 다시 확인한다.

```java
colors.add("Orange");
colors.add("Violet");
colors.add("Pink");

color = colors.get(1);
```

이번에는 get 메서드가 "Black"을 반환한다. 이것은 큐가 5개의 제한된 크기로 생성되었고 처음 3개의 요소("Red", "Blue", "Green")가 새로운 요소의 추가와 함께 제거되었기 때문이다.

### 4) Poll Method
poll 메소드는 큐의 헤드 요소를 제거하고 해당 요소를 반환 한다. 대기열에 요소가 없으면 poll 메서드는 null을 반환한다.

```java
colorsHead = colors.poll();
```

### 5) Remove Method
remove 메서드는 poll 메서드와 매우 유사하게 작동한다. 큐의 헤드를 반환하고 반환된 요소를 제거한다. 그러나 대기열이 비어 있으면 remove에서 예외가 발생한다.

```java
colorsHead = colors.remove();
```

### 6) Clear Method
큐를 비우고 싶을 때 clear 메소드를 사용할 수 있다.

```java
colors.clear();
```

## 4. 확인방법
대기열의 요소를 추가, 제거 및 검색하는 방법을 살펴본 후 클래스가 크기 및 용량 확인과 관련하여 무엇을 제공해야 하는지 확인한다. 예제에서는 이전에 만든 대기열을 사용한다.

일반적으로 대기열의 크기를 확인하는 두 가지 방법을 마음대로 사용할 수 있다. 하나는 개체의 최대 크기를 가져오는 방법이고 다른 하나는 현재 요소 수를 확인하는 방법이다.

maxSize 메서드는 큐 최대 크기의 정수 값을 반환 한다.

```java
int maxSize = bits.maxSize();
```

비트 대기열이 기본 생성자로 생성되었으므로 32를 반환한다.

size 메서드는 현재 큐에 저장된 요소의 수를 반환한다.

```java
int size = colors.size();
```

대기열 개체의 용량을 확인하기 위해 isEmpty 및 isAtFullCapacity 메서드를 사용할 수 있다.

isEmpty 메소드는 큐가 비어 있는지 여부를 나타내는 boolean 값을 반환 한다.

```java
boolean isEmpty = bits.isEmpty();
```

대기열이 꽉 찼는지 확인하려면 isAtFullCapacity 메서드를 사용할 수 있다. 이 메서드는 대기열에 있는 요소의 최대 크기에 도달한 경우에만 true를 반환한다.

```java
boolean isFull = daysOfWeek.isAtFullCapacity();
```

이 방법은 버전 4.1부터 사용할 수 있다.

대기열이 가득 찼는지 확인하는 데 사용할 수 있는 Queue 인터페이스의 또 다른 메서드는 isFull 메서드이다. CircularFifoQueue의 경우 isFull 메서드는 항상 false를 반환한다. 대기열이 항상 새 요소를 수락할 수 있기 때문이다.

```java
boolean isFull = daysOfWeek.isFull();
```

## [출처 및 참고]
* [https://www.baeldung.com/commons-circular-fifo-queue](https://www.baeldung.com/commons-circular-fifo-queue)
