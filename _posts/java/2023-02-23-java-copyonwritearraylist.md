---
title: Java CopyOnWriteArrayList
author: dejavuhyo
date: 2023-02-23 21:25:00 +0900
categories: [Language, Java]
tags: [java-copyonwritearraylist, copyonwritearraylist, java-list, list, 자바-리스트, 리스트]
---

## 1. CopyOnWriteArrayList
다중 스레드 프로그램에서 명시적인 동기화 없이 스레드로부터 안전한 방식으로 목록을 반복하려는 경우에 매우 유용하다.

## 2. CopyOnWriteArrayList API
CopyOnWriteArrayList의 디자인은 흥미로운 기술을 사용하여 동기화할 필요 없이 스레드로부터 안전하게 만든다. `add()` 또는 `remove()`와 같은 수정 메서드를 사용하는 경우 CopyOnWriteArrayList 의 전체 내용이 새 내부 복사본에 복사된다.

이 간단한 사실로 인해 동시 수정이 발생하는 경우에도 안전한 방식으로 목록을 반복할 수 있다.

CopyOnWriteArrayList에서 `iterator()` 메서드를 호출할 때 CopyOnWriteArrayList 콘텐츠의 변경 불가능한 스냅샷으로 백업된 Iterator를 다시 가져온다.

그 내용은 Iterator가 생성된 시점부터 ArrayList 내부에 있는 데이터의 정확한 복사본이다. 그동안 다른 스레드가 목록에서 요소를 추가하거나 제거하더라도 해당 수정은 해당 목록에서 추가 데이터 조회에 사용될 데이터의 새로운 복사본을 만드는 것이다.

이 데이터 구조의 특성은 수정하는 것보다 더 자주 반복하는 경우에 특히 유용합니다. 시나리오에서 요소 추가가 일반적인 작업인 경우 CopyOnWriteArrayList 는 좋은 선택이 아닙니다.

## 3. 삽입하는 동안 CopyOnWriteArrayList 반복
정수를 저장 하는 CopyOnWriteArrayList의 인스턴스를 생성한다고 가정한다.

```java
CopyOnWriteArrayList<Integer> numbers = new CopyOnWriteArrayList<>(new Integer[]{1, 3, 5, 8});
```

다음으로 해당 배열을 반복하고 싶으므로 Iterator 인스턴스를 만든다.

```java
Iterator<Integer> iterator = numbers.iterator();
```

Iterator가 생성된 후 숫자 목록에 새 요소를 추가한다.

```java
numbers.add(10);
```

CopyOnWriteArrayList에 대한 반복자를 만들 때 `iterator()`가 호출된 시점에 목록에 있는 데이터의 변경 불가능한 스냅샷을 얻는다는 점을 명심한다.

그 때문에 반복하는 동안 반복에서 숫자 10을 볼 수 없다.

```java
List<Integer> result = new LinkedList<>();
iterator.forEachRemaining(result::add);
 
assertThat(result).containsOnly(1, 3, 5, 8);
```

새로 생성된 Iterator를 사용하는 후속 반복은 추가된 숫자 10도 반환한다.

```java
Iterator<Integer> iterator2 = numbers.iterator();
List<Integer> result2 = new LinkedList<>();
iterator2.forEachRemaining(result2::add);

assertThat(result2).containsOnly(1, 3, 5, 8, 10);
```

## 4. 반복하는 동안 제거가 허용되지 않음
CopyOnWriteArrayList는 기본 목록이 수정되는 경우에도 요소를 안전하게 반복할 수 있도록 만들어졌다.

복사 메커니즘으로 인해 반환된 Iterator에 대한 `remove()` 작업이 허용되지 않는다. 결과적으로 UnsupportedOperationException이 발생합니다.

```java
@Test(expected = UnsupportedOperationException.class)
public void whenIterateOverItAndTryToRemoveElement_thenShouldThrowException() {
    
    CopyOnWriteArrayList<Integer> numbers
      = new CopyOnWriteArrayList<>(new Integer[]{1, 3, 5, 8});

    Iterator<Integer> iterator = numbers.iterator();
    while (iterator.hasNext()) {
        iterator.remove();
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-copy-on-write-arraylist](https://www.baeldung.com/java-copy-on-write-arraylist)
