---
title: Java List에서 특정 값의 모든 항목 제거
author: dejavuhyo
date: 2023-03-14 13:15:00 +0900
categories: [Language, Java]
tags: [java-list-remove-value, list-remove-value, java-list, 자바-리스트-값-제거, 리스트-값-제거, 자바-리스트]
---

## 1. while 루프 사용
단일 요소를 제거하는 방법을 알고 있으므로 루프에서 반복적으로 수행하는 것은 간단해 보인다.

```java
void removeAll(List<Integer> list, int element) {
    while (list.contains(element)) {
        list.remove(element);
    }
}
```

그러나 예상대로 작동하지 않는다.

```java
// given
List<Integer> list = list(1, 2, 3);
int valueToRemove = 1;

// when
assertThatThrownBy(() -> removeAll(list, valueToRemove))
  .isInstanceOf(IndexOutOfBoundsException.class);
```

문제는 세 번째 줄에 있다. `List.remove(int)`를 호출한다. **`List.remove(int)`는 인수를 제거하려는 값이 아니라 인덱스로 취급한다.**

위의 테스트에서는 항상 `list.remove(1)`를 호출하지만 제거하려는 요소의 인덱스는 `0`이다. `List.remove()`를 호출하면 제거된 요소 뒤의 모든 요소가 더 작은 인덱스로 이동한다.

이 시나리오에서는 첫 번째 요소를 제외한 모든 요소를 삭제한다는 의미이다.

첫 번째만 남으면 인덱스 `1`이 불법이 된다. 따라서 우리는 예외를 얻는다.

컴파일러가 일치하는 오버로드된 메서드를 찾으려고 시도할 때 가장 먼저 수행하는 작업이 넓어지기 때문에 기본 `byte`, `short`, `char` 또는 `int` 인수를 사용하여 `List.remove()`를 호출하는 경우에만 이 문제에 직면한다.

값을 Integer로 전달하여 수정할 수 있다.

```java
void removeAll(List<Integer> list, Integer element) {
    while (list.contains(element)) {
        list.remove(element);
    }
}
```

이제 코드가 예상대로 작동한다.

```java
// given
List<Integer> list = list(1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(2, 3));
```

`List.contains()` 및 `List.remove()` 둘 다 요소의 첫 번째 항목을 찾아야 하므로 이 코드는 불필요한 요소 순회를 유발한다.

첫 번째 발생의 인덱스를 저장하면 더 잘할 수 있다.

```java
void removeAll(List<Integer> list, Integer element) {
    int index;
    while ((index = list.indexOf(element)) >= 0) {
        list.remove(index);
    }
}
```

작동하는지 확인할 수 있다.

```java
// given
List<Integer> list = list(1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(2, 3));
```

이러한 솔루션은 짧고 깨끗한 코드를 생성하지만, **여전히 성능이 좋지 않다.** 진행 상황을 추적하지 않기 때문에 `List.remove()`는 제공된 값의 첫 번째 발생을 찾아 삭제해야 합니다.

또한 `ArrayList`를 사용할 때 요소 이동으로 인해 많은 참조 복사가 발생할 수 있으며 백업 배열을 여러 번 재할당하는 경우도 있다.

## 2. List가 변경될 때까지 제거
`List.remove(E element)`에는 아직 언급하지 않은 기능이 있다. boolean 값을 반환한다. 작업으로 인해 List가 변경된 경우 true이므로 요소를 포함한다.

`List.remove(int index)`는 제공된 인덱스가 유효한 경우 List가 항상 인덱스를 제거하기 때문에 void를 반환한다. 그렇지 않으면 `IndexOutOfBoundsException`이 발생한다.

이를 통해 목록이 변경될 때까지 제거를 수행할 수 있다.

```java
void removeAll(List<Integer> list, int element) {
    while (list.remove(element));
}
```

예상대로 작동한다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(2, 3));
```

짧음에도 불구하고 이 구현은 이전 섹션에서 설명한 것과 동일한 문제를 겪고 있다.

## 3. for 루프 사용
for 루프를 사용하여 요소를 순회하여 진행 상황을 추적하고 일치하는 경우 현재 요소를 제거할 수 있다.

```java
void removeAll(List<Integer> list, int element) {
    for (int i = 0; i < list.size(); i++) {
        if (Objects.equals(element, list.get(i))) {
            list.remove(i);
        }
    }
}
```

예상대로 작동한다.

```java
// given
List<Integer> list = list(1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(2, 3));
```

그러나 다른 입력으로 시도하면 잘못된 출력이 제공된다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(1, 2, 3));
```

코드가 어떻게 작동하는지 단계별로 분석해본다.

* i = 0
  - element와 `list.get(i)`는 3행에서 모두 1 이므로 Java는 if 문의 본문에 들어간다.
  - 인덱스 0 에서 요소를 제거한다.
  - 이제 List에는 1, 2 및 3이 포함된다.

* i = 1
  - `list.get(i)`는 List에서 요소를 제거할 때 모든 진행 요소를 더 작은 인덱스로 이동하기 때문에 2를 반환한다.

따라서 제거하려는 두 개의 인접한 값이 있을 때 이 문제에 직면한다. 이를 해결하기 위해서는 루프 변수를 유지해야 한다.

요소를 제거할 때 감소

```java
void removeAll(List<Integer> list, int element) {
    for (int i = 0; i < list.size(); i++) {
        if (Objects.equals(element, list.get(i))) {
            list.remove(i);
            i--;
        }
    }
}
```

요소를 제거하지 않을 때만 늘린다.

```java
void removeAll(List<Integer> list, int element) {
    for (int i = 0; i < list.size();) {
        if (Objects.equals(element, list.get(i))) {
            list.remove(i);
        } else {
            i++;
        }
    }
}
```

후자의 경우 2행에서 `i++`문을 제거했다.

두 솔루션 모두 예상대로 작동한다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(2, 3));
```

이 구현은 옳은 것 같다. 그러나 여전히 심각한 **성능 문제**가 있다.

* ArrayList에서 요소를 제거하고 그 뒤의 모든 항목을 이동한다.

* LinkedList에서 인덱스로 요소에 액세스한다는 것은 인덱스를 찾을 때까지 요소를 하나씩 순회하는 것을 의미한다.

## 4. for-each 루프 사용
Java 5부터 `for-each` 루프를 사용하여 List를 반복 할 수 있다. 이를 사용하여 요소를 제거해 본다.

```java
void removeAll(List<Integer> list, int element) {
    for (Integer number : list) {
        if (Objects.equals(number, element)) {
            list.remove(number);
        }
    }
}
```

루프 변수의 유형으로 `Integer`를 사용한다. 따라서 `NullPointerException`을 얻지 못할 것이다.

또한 이렇게 하면 인덱스가 아니라 제거하려는 값을 예상하는 `List.remove(E element)`를 호출한다.

보기에는 깨끗해 보이지만 안타깝게도 작동하지 않는다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
assertThatThrownBy(() -> removeWithForEachLoop(list, valueToRemove))
  .isInstanceOf(ConcurrentModificationException.class);
```

`for-each` 루프는 Iterator를 사용하여 요소를 순회한다. 그러나 **List를 수정하면 Iterator가 일관성 없는 상태가 된다.** 따라서 `ConcurrentModificationException`이 발생한다.

교훈은 `for-each` 루프 에서 해당 요소에 액세스하는 동안 List를 수정해서는 안 된다는 것이다.

## 5. Iterator 사용
Iterator를 직접 사용하여 List를 순회하고 수정할 수 있다.

```java
void removeAll(List<Integer> list, int element) {
    for (Iterator<Integer> i = list.iterator(); i.hasNext();) {
        Integer number = i.next();
        if (Objects.equals(number, element)) {
            i.remove();
        }
    }
}
```

이렇게 하면 Iterator가 List의 상태를 추적할 수 있다 (수정하기 때문). 결과적으로 위의 코드는 예상대로 작동한다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(2, 3));
```

모든 List 클래스는 자체 Iterator 구현을 제공할 수 있으므로 **가장 효율적인 방법으로 요소 순회 및 제거**를 구현한다고 가정할 수 있다.

그러나 **ArrayList를 사용한다는 것은 여전히 많은 요소 이동 (및 배열 재할당)을 의미**한다. 또한 위의 코드는 대부분의 개발자에게 익숙한 표준 for 루프와 다르기 때문에 읽기가 약간 더 어렵다.

## 6. Collecting
여기까지는 필요하지 않은 항목을 제거하여 원래 List 개체를 수정했다. 오히려 새 목록을 만들고 유지하려는 항목을 수집할 수 있다.

```java
List<Integer> removeAll(List<Integer> list, int element) {
    List<Integer> remainingElements = new ArrayList<>();
    for (Integer number : list) {
        if (!Objects.equals(number, element)) {
            remainingElements.add(number);
        }
    }
    return remainingElements;
}
```

새로운 List 객체에 결과를 제공하기 때문에 메서드에서 반환해야 한다. 따라서 이 방법을 다른 방식으로 사용해야 한다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
List<Integer> result = removeAll(list, valueToRemove);

// then
assertThat(result).isEqualTo(list(2, 3));
```

현재 반복하고 있는 목록을 수정하지 않기 때문에 이제 `for-each` 루프를 사용할 수 있다.

제거가 없기 때문에 요소를 이동할 필요가 없다. 따라서 이 구현은 ArrayList를 사용할 때 잘 수행된다.

이 구현은 몇 가지 방식에서 이전 구현과 다르게 동작한다.

* 원래 목록을 수정하지 않고 새 목록을 반환한다.

* 메서드는 반환된 List의 구현이 무엇인지 결정하며 원래와 다를 수 있다.

또한 구현을 수정하여 이전 동작을 얻을 수 있다. 원래 목록을 지우고 수집된 요소를 추가한다.

```java
void removeAll(List<Integer> list, int element) {
    List<Integer> remainingElements = new ArrayList<>();
    for (Integer number : list) {
        if (!Objects.equals(number, element)) {
            remainingElements.add(number);
        }
    }

    list.clear();
    list.addAll(remainingElements);
}
```

이전과 동일한 방식으로 작동한다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(2, 3));
```

List를 지속적으로 수정하지 않기 때문에 위치별로 요소에 액세스하거나 요소를 이동할 필요가 없다. 또한 가능한 array 재할당은 `List.clear()` 및 `List.addAll()`을 호출할 때 두 가지뿐이다.

## 7. Stream API 사용
Java 8은 람다 식과 스트림 API를 도입했다. 이러한 강력한 기능을 통해 매우 깔끔한 코드로 문제를 해결할 수 있다.

```java
List<Integer> removeAll(List<Integer> list, int element) {
    return list.stream()
      .filter(e -> !Objects.equals(e, element))
      .collect(Collectors.toList());
}
```

이 솔루션은 나머지 요소를 수집할 때와 동일한 방식으로 작동한다.

결과적으로 동일한 특성을 가지며 이를 사용하여 결과를 반환해야 한다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
List<Integer> result = removeAll(list, valueToRemove);

// then
assertThat(result).isEqualTo(list(2, 3));
```

원래 'collecting' 구현과 동일한 접근 방식을 사용하여 다른 솔루션처럼 작동하도록 변환할 수 있다.

## 8. removeIf 사용
람다 및 기능적 인터페이스와 함께 Java 8은 일부 API 확장도 도입했다. 예를 들어 `List.removeIf()` 메서드는 지난 섹션에서 본 것을 구현한다.

요소를 유지하려고 할 때 true를 반환해야 했던 이전 예제와 달리 요소를 제거하려고 할 때 true를 반환해야 하는 Predicate를 기대한다.

```java
void removeAll(List<Integer> list, int element) {
    list.removeIf(n -> Objects.equals(n, element));
}
```

위의 다른 솔루션처럼 작동한다.

```java
// given
List<Integer> list = list(1, 1, 2, 3);
int valueToRemove = 1;

// when
removeAll(list, valueToRemove);

// then
assertThat(list).isEqualTo(list(2, 3));
```

List 자체가 이 메서드를 구현한다는 사실 때문에 최상의 성능을 제공한다고 가정할 수 있다. 무엇보다 이 솔루션은 가장 깨끗한 코드를 제공한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-remove-value-from-list](https://www.baeldung.com/java-remove-value-from-list)
