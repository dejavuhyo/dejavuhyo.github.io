---
title: Java Convenience Factory 메서드
author: dejavuhyo
date: 2023-06-21 08:50:00 +0900
categories: [Language, Java]
tags: [java-collection, convenience-factory-methods, 자바-컬렉션, 컨비넌스-펙토리-메서드]
---

## 1. 연혁 및 동기
Java에서 불변의 작은 컬렉션을 만드는 것은 전통적인 방식을 사용하는 매우 장황한 작업이다.

Set의 예이다.

```java
Set<String> set = new HashSet<>();
set.add("foo");
set.add("bar");
set.add("baz");
set = Collections.unmodifiableSet(set);
```

이는 간단한 작업을 수행하기에는 너무 많은 코드이며 단일 표현식으로 수행할 수 있어야 한다.

위의 내용은 Map의 경우에도 마찬가지이다.

그러나 List에는 팩토리 메서드가 있다.

```java
List<String> list = Arrays.asList("foo", "bar", "baz");
```

이 List 생성이 생성자 초기화보다 낫지만 일반적인 직관은 List를 생성하는 메서드에 대해 Arrays 클래스를 조사하는 것이 아니기 때문에 명확하지 않다.

이중 중괄호 초기화 기술과 같이 장황함을 줄이는 다른 방법이 있다.

```java
{% raw %}
Set<String> set = Collections.unmodifiableSet(new HashSet<String>() {{
    add("foo"); add("bar"); add("baz");
}});
{% endraw %}
```

또는 Java 8 스트림을 사용한다.

```java
Stream.of("foo", "bar", "baz").collect(collectingAndThen(toSet(), Collections::unmodifiableSet));
```

이중 중괄호 기술은 약간 덜 장황하지만 가독성을 크게 떨어뜨린다(반패턴으로 간주됨).

그러나 Java 8 버전은 한 줄로 된 표현이며 몇 가지 문제도 있다. 첫째, 명확하고 직관적이지 않다. 둘째, 여전히 장황다. 셋째, 불필요한 객체의 생성을 수반한다. 넷째, 이 메서드는 Map을 만드는 데 사용할 수 없다.

단점을 요약하면 위의 접근 방식 중 어느 것도 수정할 수 없는 작은 Collection 일급 클래스 문제를 생성하는 특정 사용 사례를 처리하지 않는다.

## 2. 설명 및 사용법
List, Set 및 Map 인터페이스에 대해 요소를 인수로 사용하고 각각 List, Set 및 Map의 인스턴스를 반환하는 정적 메서드가 제공되었다.

이 메서드는 세 인터페이스 모두에 대해 `of(…)`로 이름이 지정된다.

### 1) List와 Set
List 및 Set 팩터리 메서드의 서명 및 특성은 동일하다.

```java
static <E> List<E> of(E e1, E e2, E e3)
static <E> Set<E>  of(E e1, E e2, E e3)
```

사용 방법이다.

```java
List<String> list = List.of("foo", "bar", "baz");
Set<String> set = Set.of("foo", "bar", "baz");
```

매우 간단하고 짧고 간결하다.

예제에서 정확히 3개의 요소를 매개변수로 취하고 크기 3의 List/Set를 반환하는 메서드를 사용했다.

그러나 이 메서드에는 12개의 오버로드된 버전이 있다. 0~10개의 매개변수가 포함된 11개와 var-args가 포함된 버전이 있다.

```java
static <E> List<E> of()
static <E> List<E> of(E e1)
static <E> List<E> of(E e1, E e2)
// ....and so on

static <E> List<E> of(E... elems)
```

대부분의 실용적인 목적을 위해 10개의 요소로 충분하지만 더 필요한 경우 var-args 버전을 사용할 수 있다.

많은 요소에 대해 작동할 수 있는 var-args 버전이 있는 경우 11개의 추가 메서드가 있는 요점을 알 수 있다.

그에 대한 답은 성능이다. 모든 var-args 메서드 호출은 암시적으로 배열을 만든다. 오버로드된 메서드를 사용하면 불필요한 객체 생성 및 가비지 수집 오버헤드를 피할 수 있다. 반대로 `Arrays.asList`는 항상 해당 암시적 배열을 생성하므로 요소 수가 적을 때 효율성이 떨어진다.

팩터리 메서드를 사용하여 집합을 만드는 동안 중복 요소가 매개 변수로 전달되면 런타임에 IllegalArgumentException이 발생한다.

```java
@Test(expected = IllegalArgumentException.class)
public void onDuplicateElem_IfIllegalArgExp_thenSuccess() {
    Set.of("foo", "bar", "baz", "foo");
}
```

주목해야 할 중요한 점은 팩토리 메소드가 제네릭을 사용하기 때문에 기본 유형이 자동 박싱된다는 것이다.

기본 유형의 배열이 전달되면 해당 기본 유형의 배열 목록이 반환된다.

예이다.

```java
int[] arr = { 1, 2, 3, 4, 5 };
List<int[]> list = List.of(arr);
```

이 경우 크기 1의 `List<int[]>`가 반환되고 인덱스 0의 요소에 배열이 포함된다.

### 2) Map
맵 팩터리 메서드의 서명은 다음과 같다.

```java
static <K,V> Map<K,V> of(K k1, V v1, K k2, V v2, K k3, V v3)
```

사용방법 이다.

```java
Map<String, String> map = Map.of("foo", "a", "bar", "b", "baz", "c");
```

List 및 Set과 유사하게 `of(…)` 메서드는 0~10개의 key-value 쌍을 갖도록 오버로드된다.

Map의 경우 10개 이상의 key-value 쌍에 대해 다른 방법이 있다.

```java
static <K,V> Map<K,V> ofEntries(Map.Entry<? extends K,? extends V>... entries)
```

사용방법 이다.

```java
Map<String, String> map = Map.ofEntries(
  new AbstractMap.SimpleEntry<>("foo", "a"),
  new AbstractMap.SimpleEntry<>("bar", "b"),
  new AbstractMap.SimpleEntry<>("baz", "c"));
```

Key에 중복 값을 전달하면 IllegalArgumentException이 발생한다.

```java
@Test(expected = IllegalArgumentException.class)
public void givenDuplicateKeys_ifIllegalArgExp_thenSuccess() {
    Map.of("foo", "a", "foo", "b");
}
```

Map의 경우에도 기본 유형은 오토박스 처리된다.

## 3. 구현 참고 사항
팩터리 메서드를 사용하여 만든 컬렉션은 일반적으로 사용되는 구현이 아니다.

예를 들어 List는 ArrayList가 아니고 Map은 HashMap이 아니다. 그것들은 Java 9에 도입된 다른 구현이다. 이러한 구현은 내부적이며 해당 생성자는 액세스가 제한된다.

세 가지 컬렉션 유형 모두에 공통적인 몇 가지 중요한 구현 차이점이 있다.

### 1) Immutable
팩터리 메서드를 사용하여 만든 컬렉션은 변경할 수 없으며 요소를 변경하거나 새 요소를 추가하거나 요소를 제거하면 UnsupportedOperationException이 발생한다.

```java
@Test(expected = UnsupportedOperationException.class)
public void onElemAdd_ifUnSupportedOpExpnThrown_thenSuccess() {
    Set<String> set = Set.of("foo", "bar");
    set.add("baz");
}
```

```java
@Test(expected = UnsupportedOperationException.class)
public void onElemModify_ifUnSupportedOpExpnThrown_thenSuccess() {
    List<String> list = List.of("foo", "bar");
    list.set(0, "baz");
}
```

```java
@Test(expected = UnsupportedOperationException.class)
public void onElemRemove_ifUnSupportedOpExpnThrown_thenSuccess() {
    Map<String, String> map = Map.of("foo", "a", "bar", "b");
    map.remove("foo");
}
```

### 2) null 요소가 허용되지 않음
List 및 Set의 경우 어떤 요소도 null이 될 수 없다. Map의 경우 키도 값도 null 일 수 없다. null 인수를 전달하면 NullPointerException이 발생한다.

```java
@Test(expected = NullPointerException.class)
public void onNullElem_ifNullPtrExpnThrown_thenSuccess() {
    List.of("foo", "bar", null);
}
```

`List.of`와 달리 `Arrays.asList` 메서드는 null 값을 허용한다.

### 3) Value-Based 인스턴스
팩토리 메소드로 생성된 인스턴스는 값 기반이다. 이는 팩토리가 자유롭게 새 인스턴스를 생성하거나 기존 인스턴스를 반환할 수 있음을 의미한다.

따라서 동일한 값으로 목록을 만들면 힙에서 동일한 객체를 참조할 수도 있고 참조하지 않을 수도 있다.

```java
List<String> list1 = List.of("foo", "bar");
List<String> list2 = List.of("foo", "bar");
```

이 경우 `list1 == list2`는 JVM에 따라 true로 평가될 수도 있고 그렇지 않을 수도 있다.

### 4) 직렬화
컬렉션의 요소가 직렬화 가능한 경우 팩터리 메서드에서 생성된 컬렉션은 직렬화 가능하다.

## [출처 및 참고]
* [https://www.baeldung.com/java-9-collections-factory-methods](https://www.baeldung.com/java-9-collections-factory-methods)
