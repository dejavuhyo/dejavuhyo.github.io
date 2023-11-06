---
title: Java 8 Collectors
author: dejavuhyo
date: 2023-11-06 22:30:00 +0900
categories: [Language, Java]
tags: [java-collectors, collectors, 자바-수집기, 수집기]
---

## 1. Stream.collect() 메서드
`Stream.collect()`는 Java 8의 Stream API 터미널 메소드 중 하나이다. 이를 통해 Stream 인스턴스에 있는 데이터 요소에 대해 변경 가능한 접기 작업(요소를 일부 데이터 구조에 다시 패키징하고 추가 논리를 적용하고 연결하는 등)을 수행할 수 있다.

이 작업에 대한 전략은 Collector 인터페이스 구현을 통해 제공된다.

## 2. Collectors
사전 정의된 모든 구현은 Collectors 클래스에서 찾을 수 있다. 향상된 가독성을 활용하기 위해 다음과 같은 정적 가져오기를 사용하는 것이 일반적인 관행이다.

```java
import static java.util.stream.Collectors.*;
```

원하는 단일 가져오기 수집기를 사용할 수도 있다.

```java
import static java.util.stream.Collectors.toList;
import static java.util.stream.Collectors.toMap;
import static java.util.stream.Collectors.toSet;
```

다음 예에서는 다음 목록을 재사용한다.

```java
List<String> givenList = Arrays.asList("a", "bb", "ccc", "dd");
```

### 1) Collectors.toList()
toList 수집기는 모든 Stream 요소를 List 인스턴스로 수집하는데 사용할 수 있다. 중요한 점은 이 메소드를 사용하여 특정 List 구현을 가정할 수 없다는 것이다. 이에 대해 더 많은 제어를 원할 경우 toCollection을 대신 사용할 수 있다.

일련의 요소를 나타내는 Stream 인스턴스를 만든 다음 이를 List 인스턴스로 수집해 본다.

```java
List<String> result = givenList.stream().collect(toList());
```

### 2) Collectors.toUnmodifiableList()
Java 10에서는 Stream 요소를 수정 불가능한 목록 에 누적하는 편리한 방법을 도입했다.

```java
List<String> result = givenList.stream().collect(toUnmodifiableList());
```

이제 결과 List를 수정하려고 하면 UnsupportedOperationException이 발생한다.

```java
assertThatThrownBy(() -> result.add("foo")).isInstanceOf(UnsupportedOperationException.class);
```

### 3) Collectors.toSet()
toSet 콜렉터는 모든 Stream 요소를 Set 인스턴스 로 수집하는데 사용할 수 있다. 중요한 점은 이 메서드를 사용하여 특정 Set 구현을 가정할 수 없다는 것이다. 이에 대해 더 많은 제어를 원할 경우 toCollection을 대신 사용할 수 있다.

일련의 요소를 나타내는 Stream 인스턴스를 만든 다음 이를 Set 인스턴스로 수집해 본다.

```java
Set<String> result = givenList.stream().collect(toSet());
```

Set에는 중복 요소가 포함되어 있지 않다. 컬렉션에 서로 동일한 요소가 포함된 경우 해당 요소는 결과 Set에 한 번만 나타난다.

```java
List<String> listWithDuplicates = Arrays.asList("a", "bb", "c", "d", "bb");
Set<String> result = listWithDuplicates.stream().collect(toSet());
assertThat(result).hasSize(4);
```

### 4) Collectors.toUnmodifyingSet()
Java 10부터 `toUnmodifyingSet()` 컬렉터를 사용하여 수정 불가능한 Set을 쉽게 생성할 수 있다.

```java
Set<String> result = givenList.stream().collect(toUnmodifiableSet());
```

결과 세트를 수정하려는 모든 시도는 UnsupportedOperationException으로 종료된다.

```java
assertThatThrownBy(() -> result.add("foo")).isInstanceOf(UnsupportedOperationException.class);
```

### 5) Collectors.toCollection()
toSet 및 toList 수집기를 사용할 때 해당 구현에 대해 어떠한 가정도 할 수 없다. 사용자 정의 구현을 사용하려면 선택한 컬렉션을 제공하는 toCollection 수집기를 사용해야 한다.

일련의 요소를 나타내는 Stream 인스턴스를 만든 다음 이를 LinkedList 인스턴스로 수집해 본다.

```java
List<String> result = givenList.stream().collect(toCollection(LinkedList::new))
```

이는 변경 불가능한 컬렉션에서는 작동하지 않는다. 그러한 경우에는 사용자 정의 Collector 구현을 작성하거나 collectionAndThen을 사용해야 한다.


### 6) Collectors.toMap()
toMap 수집기는 Stream 요소를 Map 인스턴스로 수집하는데 사용할 수 있다. 이를 위해서는 두 가지 기능을 제공해야 한다.

* keyMapper

* valueMapper

keyMapper를 사용하여 Stream 요소에서 Map 키를 추출 하고 valueMapper를 사용하여 특정 키와 관련된 값을 추출한다.

문자열을 키로, 길이를 값으로 저장하는 Map으로 해당 요소를 수집해 본다.

```java
Map<String, Integer> result = givenList.stream().collect(toMap(Function.identity(), String::length))
```

`Function.identity()`는 동일한 값을 받아들이고 반환하는 함수를 정의하는 간단한 방법이다.

그렇다면 컬렉션에 중복된 요소가 포함되어 있으면 toSet과 달리 toMap은 중복 항목을 자동으로 필터링하지 않는다. 이는 이 키에 대해 어떤 값을 선택할지 어떻게 알아낼 수 있기 때문에 이해할 수 있는 것이다.

```java
List<String> listWithDuplicates = Arrays.asList("a", "bb", "c", "d", "bb");
assertThatThrownBy(() -> {
    listWithDuplicates.stream().collect(toMap(Function.identity(), String::length));
}).isInstanceOf(IllegalStateException.class);
```

toMap은 값이 동일한지 여부도 평가하지 않는다. 중복된 키가 발견되면 즉시 IllegalStateException이 발생한다.

키 충돌이 있는 경우에는 다른 서명과 함께 toMap을 사용해야 한다.

```java
Map<String, Integer> result = givenList.stream().collect(toMap(Function.identity(), String::length, (item, identicalItem) -> item));
```

여기서 세 번째 인수는 충돌 처리 방법을 지정할 수 있는 BinaryOperator 이다. 이 경우 동일한 문자열은 항상 동일한 길이를 갖기 때문에 충돌하는 두 값 중 하나를 선택한다.

### 7) Collectors.toUnmodifyingMap()
List 및 Set과 마찬가지로 Java 10에서는 Stream 요소를 수정 불가능한 Map으로 수집하는 쉬운 방법을 도입했다.

```java
Map<String, Integer> result = givenList.stream().collect(toUnmodifiableMap(Function.identity(), String::length))
```

Map에 새 항목을 넣으려고 하면 UnsupportedOperationException이 발생한다.

```java
assertThatThrownBy(() -> result.put("foo", 3)).isInstanceOf(UnsupportedOperationException.class);
```

### 8) Collectors.collectingAndThen()
CollectingAndThen은 끝을 수집한 직후 결과에 대해 다른 작업을 수행할 수 있는 특수 수집기이다.

Stream 요소를 List 인스턴스로 수집한 다음 결과를 ImmutableList 인스턴스로 변환해 본다.

```java
List<String> result = givenList.stream().collect(collectingAndThen(toList(), ImmutableList::copyOf))
```

### 9) Collectors.joining()
Joining 컬렉터는 `Stream<String>` 요소를 조인하는데 사용될 수 있다.

우리는 다음을 수행하여 그것들을 함께 결합할 수 있다.

```java
String result = givenList.stream().collect(joining());
```

결과는 다음과 같다.

```text
"abbcccdd"
```

사용자 정의 구분 기호, 접두사, 접미사를 지정할 수도 있다.

```java
String result = givenList.stream().collect(joining(" "));
```

결과는 다음과 같다.

```text
"a bb ccc dd"
```

다음과 같이 작성할 수도 있다.

```java
String result = givenList.stream().collect(joining(" ", "PRE-", "-POST"));
```

결과는 다음과 같다.

```text
"PRE-a bb ccc dd-POST"
```

### 10) Collectors.counting()
Counting은 모든 Stream 요소의 계산을 허용하는 간단한 수집기이다.

이제 다음과 같이 작성할 수 있다.

```java
Long result = givenList.stream().collect(counting());
```

### 11) Collectors.summarizingDouble/Long/Int()
SummarizingDouble/Long/Int는 추출된 요소 스트림의 수치 데이터에 대한 통계 정보가 포함된 특수 클래스를 반환하는 수집기이다.

다음을 수행하여 문자열 길이에 대한 정보를 얻을 수 있다.

```java
DoubleSummaryStatistics result = givenList.stream().collect(summarizingDouble(String::length));
```

이 경우 다음이 적용된다.

```java
assertThat(result.getAverage()).isEqualTo(2);
assertThat(result.getCount()).isEqualTo(4);
assertThat(result.getMax()).isEqualTo(3);
assertThat(result.getMin()).isEqualTo(1);
assertThat(result.getSum()).isEqualTo(8);
```

### 12) Collectors.averagingDouble/Long/Int()
AveragingDouble/Long/Int는 추출된 요소들의 평균을 단순히 반환하는 컬렉터이다.

다음을 수행하여 평균 문자열 길이를 얻을 수 있다.

```java
Double result = givenList.stream().collect(averagingDouble(String::length));
```

### 13) Collectors.summingDouble/Long/Int()
SummingDouble/Long/Int는 추출된 요소의 합을 단순히 반환하는 컬렉터이다.

다음을 수행하여 모든 문자열 길이의 합계를 얻을 수 있다.

```java
Double result = givenList.stream().collect(summingDouble(String::length));
```

### 14) Collectors.maxBy()/minBy()
MaxBy/MinBy 수집기는 제공된 Comparator 인스턴스에 따라 Stream의 가장 큰/가장 작은 요소를 반환한다.

다음을 수행하여 가장 큰 요소를 선택할 수 있다.

```java
Optional<String> result = givenList.stream().collect(maxBy(Comparator.naturalOrder()));
```

반환된 값이 Optional 인스턴스에 래핑되어 있음을 볼 수 있다. 이로 인해 사용자는 빈 컬렉션 코너 케이스를 다시 생각하게 된다.

### 15) Collectors.groupingBy()
GroupingBy 컬렉터는 일부 속성을 기준으로 객체를 그룹화한 다음 결과를 Map 인스턴스에 저장하는데 사용된다.

문자열 길이별로 그룹화하고 그룹화 결과를 Set 인스턴스에 저장할 수 있다.

```java
Map<Integer, Set<String>> result = givenList.stream().collect(groupingBy(String::length, toSet()));
```

결과는 다음과 같다.

```java
assertThat(result)
  .containsEntry(1, newHashSet("a"))
  .containsEntry(2, newHashSet("bb", "dd"))
  .containsEntry(3, newHashSet("ccc"));
```

groupingBy 메소드의 두 번째 인수가 Collector임을 알 수 있다. 또한 원하는 수집기를 자유롭게 사용할 수 있다.

### 16) Collectors.partitioningBy()
PartitioningBy는 Predicate 인스턴스를 허용한 다음 부울 값을 키로 저장하고 컬렉션을 값으로 저장하는 Map 인스턴스로 Stream 요소를 수집하는 groupingBy의 특수한 사례이다. "true" 키 아래에서는 주어진 Predicate와 일치하는 요소 모음을 찾을 수 있고, "false" 키 아래에서는 주어진 Predicate와 일치하지 않는 요소 모음을 찾을 수 있다.

다음과 같이 쓸 수 있다.

```java
Map<Boolean, List<String>> result = givenList.stream().collect(partitioningBy(s -> s.length() > 2))
```

결과적으로 다음을 포함하는 Map이 생성된다.

```text
{false=["a", "bb", "dd"], true=["ccc"]}
```

### 17) Collectors.teeing()
지금까지 배운 수집기를 사용하여 특정 스트림에서 최대값과 최소값을 찾아보겠다.

```java
List<Integer> numbers = Arrays.asList(42, 4, 2, 24);
Optional<Integer> min = numbers.stream().collect(minBy(Integer::compareTo));
Optional<Integer> max = numbers.stream().collect(maxBy(Integer::compareTo));
// do something useful with min and max
```

여기서는 두 개의 서로 다른 수집기를 사용한 다음 이 두 가지의 결과를 결합하여 의미 있는 것을 만든다. Java 12 이전에는 이러한 사용 사례를 처리하기 위해 지정된 스트림에서 두 번 작업하고 중간 결과를 임시 변수에 저장한 다음 나중에 해당 결과를 결합해야 했다.

다행스럽게도 Java 12는 이러한 단계를 처리하는 내장 수집기를 제공한다. 두 개의 수집기와 결합기 기능을 제공하는 것뿐이다.

이 새로운 컬렉터는 주어진 스트림을 서로 다른 두 방향으로 teeing 하므로 이를 teeing이라고 한다.

```java
numbers.stream().collect(teeing(
  minBy(Integer::compareTo), // The first collector
  maxBy(Integer::compareTo), // The second collector
  (min, max) -> // Receives the result from those collectors and combines them
));
```

4. 맞춤형 Collectors
자체 Collector 구현을 작성하려면 Collector 인터페이스를 구현하고 세 가지 일반 매개변수를 지정해야 합니다.

```java
public interface Collector<T, A, R> {...}
```

* T - 수집에 사용할 수 있는 객체 유형

* A - 변경 가능한 누산기 객체의 유형

* R - 최종 결과의 유형

ImmutableSet 인스턴스에 요소를 수집하기 위한 예제 Collector를 작성해 본다. 올바른 유형을 지정하는 것부터 시작한다.

```java
private class ImmutableSetCollector<T> implements Collector<T, ImmutableSet.Builder<T>, ImmutableSet<T>> {...}
```

내부 컬렉션 작업 처리를 위해 변경 가능한 컬렉션이 필요하므로 ImmutableSet을 사용할 수 없다. 대신, 변경 가능한 다른 컬렉션이나 일시적으로 객체를 축적할 수 있는 다른 클래스를 사용해야 한다. 이 경우 `ImmutableSet.Builder`를 사용 하고 이제 5가지 메서드를 구현해야 한다.

* `Supplier<ImmutableSet.Builder<T>> supplier()`

* `BiConsumer<ImmutableSet.Builder<T>, T> accumulator()`

* `BinaryOperator<ImmutableSet.Builder<T>> combiner()`

* `Function<ImmutableSet.Builder<T>, ImmutableSet<T>> finisher()`

* `Set<Characteristics> characteristics()`

supplier() 메서드는 빈 Supplier 인스턴스를 생성하는 공급자 인스턴스를 반환한다. 따라서 이 경우 간단히 다음과 같이 작성할 수 있다.

```java
@Override
public Supplier<ImmutableSet.Builder<T>> supplier() {
    return ImmutableSet::builder;
}
```

`accumulator()` 메서드는 기존 accumulator 개체에 새 요소를 추가하는데 사용되는 함수를 반환한다. 이제 Builder의 add 메소드를 사용해 본다.

```java
@Override
public BiConsumer<ImmutableSet.Builder<T>, T> accumulator() {
    return ImmutableSet.Builder::add;
}
```

`combiner()` 메서드는 두 개의 accumulators를 병합하는데 사용되는 함수를 반환한다.

```java
@Override
public BinaryOperator<ImmutableSet.Builder<T>> combiner() {
    return (left, right) -> left.addAll(right.build());
}
```

`finisher()` 메서드는 누산기를 최종 결과 유형으로 변환하는데 사용되는 함수를 반환한다. 따라서 이 경우에는 Builder의 빌드 방법을 사용한다.

```java
@Override
public Function<ImmutableSet.Builder<T>, ImmutableSet<T>> finisher() {
    return ImmutableSet.Builder::build;
}
```

`characteristics()` 메서드는 Stream에 내부 최적화에 사용될 몇 가지 추가 정보를 제공하는데 사용된다. 이 경우 `Characteristics.UNORDERED`를 사용하기 때문에 Set의 요소 순서에 주의를 기울이지 않는다.

```java
@Override public Set<Characteristics> characteristics() {
    return Sets.immutableEnumSet(Characteristics.UNORDERED);
}
```

사용법과 함께 완전한 구현은 다음과 같다.

```java
public class ImmutableSetCollector<T>
  implements Collector<T, ImmutableSet.Builder<T>, ImmutableSet<T>> {

@Override
public Supplier<ImmutableSet.Builder<T>> supplier() {
    return ImmutableSet::builder;
}

@Override
public BiConsumer<ImmutableSet.Builder<T>, T> accumulator() {
    return ImmutableSet.Builder::add;
}

@Override
public BinaryOperator<ImmutableSet.Builder<T>> combiner() {
    return (left, right) -> left.addAll(right.build());
}

@Override
public Function<ImmutableSet.Builder<T>, ImmutableSet<T>> finisher() {
    return ImmutableSet.Builder::build;
}

@Override
public Set<Characteristics> characteristics() {
    return Sets.immutableEnumSet(Characteristics.UNORDERED);
}

public static <T> ImmutableSetCollector<T> toImmutableSet() {
    return new ImmutableSetCollector<>();
}
```

마지막으로 실제로 실행되는 내용은 다음과 같다.

```java
List<String> givenList = Arrays.asList("a", "bb", "ccc", "dddd");

ImmutableSet<String> result = givenList.stream().collect(toImmutableSet());
```

## [출처 및 참고]
* [https://www.baeldung.com/java-8-collectors](https://www.baeldung.com/java-8-collectors)
