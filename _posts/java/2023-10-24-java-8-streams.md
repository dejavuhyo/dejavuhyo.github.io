---
title: Java 8 Stream API
author: dejavuhyo
date: 2023-10-24 22:20:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-api, stream, 자바-스트림, 스트림]
---

## 1. 스트림 생성
다양한 소스의 스트림 인스턴스를 생성하는 방법에는 여러 가지가 있다. 일단 생성된 인스턴스는 소스를 수정하지 않으므로 단일 소스에서 여러 인스턴스를 생성할 수 있다.

### 1) Empty Stream
빈 스트림을 생성하는 경우에는 `empty()` 메서드를 사용해야 한다.

```java
Stream<String> streamEmpty = Stream.empty();
```

요소가 없는 스트림에 대해 null이 반환되는 것을 방지하기 위해 생성 시 종종 `empty()` 메서드를 사용한다.

```java
public Stream<String> streamOf(List<String> list) {
    return list == null || list.isEmpty() ? Stream.empty() : list.stream();
}
```

### 2) Stream of Collection
모든 유형의 컬렉션 (Collection, List, Set) 스트림을 생성할 수도 있다.

```java
Collection<String> collection = Arrays.asList("a", "b", "c");
Stream<String> streamOfCollection = collection.stream();
```

### 3) Stream of Array
배열은 스트림의 소스가 될 수도 있다.

```java
Stream<String> streamOfArray = Stream.of("a", "b", "c");
```

기존 배열이나 배열의 일부에서 스트림을 만들 수도 있다.

```java
String[] arr = new String[]{"a", "b", "c"};
Stream<String> streamOfArrayFull = Arrays.stream(arr);
Stream<String> streamOfArrayPart = Arrays.stream(arr, 1, 3);
```

### 4) Stream.builder()
builder를 사용하는 경우 문의 오른쪽 부분에 원하는 유형을 추가로 지정해야 한다. 그렇지 않으면 `build()` 메서드가 `Stream<Object>`의 인스턴스를 생성한다.

```java
Stream<String> streamBuilder = Stream.<String>builder().add("a").add("b").add("c").build();
```

### 5) Stream.generate()
`generate()` 메소드는 요소 생성을 위해 `Manufacturer<T>`를 허용한다. 결과 스트림은 무한하므로 개발자는 원하는 크기를 지정해야 한다. 그렇지 않으면 `generate()` 메서드는 메모리 제한에 도달할 때까지 작동한다.

```java
Stream<String> streamGenerated = Stream.generate(() -> "element").limit(10);
```

위의 코드는 값이 "element"인 10개의 문자열 시퀀스를 생성한다.

### 6) Stream.iterate()
무한 스트림을 생성하는 또 다른 방법은 `iterate()` 메서드를 사용하는 것이다.

```java
Stream<Integer> streamIterated = Stream.iterate(40, n -> n + 2).limit(20);
```

결과 스트림의 첫 번째 요소는 `iterate()` 메서드의 첫 번째 매개 변수이다. 다음 요소를 모두 생성하면 지정된 기능이 이전 요소에 적용된다. 위의 예에서 두 번째 요소는 42이다.

### 7) Stream of Primitives
Java 8은 int, long 및 double의 세 가지 기본 유형에서 스트림을 생성할 수 있는 가능성을 제공한다. `Stream<T>`은 제네릭 인터페이스이고 제네릭의 유형 매개변수로 프리미티브를 사용할 수 있는 방법이 없으므로 IntStream, LongStream, DoubleStream이라는 세 가지 새로운 특수 인터페이스가 생성되었다.

새로운 인터페이스를 사용하면 불필요한 자동 박싱이 줄어들어 생산성이 향상된다.

```java
IntStream intStream = IntStream.range(1, 3);
LongStream longStream = LongStream.rangeClosed(1, 3);
```

`range(int startInclusive, int endExclusive)` 메서드는 첫 번째 매개변수부터 두 번째 매개변수까지 순서가 지정된 스트림을 생성한다. 단계가 1이 되도록 후속 요소의 값을 증가시킨다. 결과에는 마지막 매개변수가 포함되지 않으며 단지 시퀀스의 상한값만 포함된다.

`rangeClosed(int startInclusive, int endInclusive)` 메서드는 한 가지 차이점만 제외하고 동일한 작업을 수행한다. 두 번째 요소가 포함된다. 이 두 가지 방법을 사용하여 세 가지 유형의 기본 스트림 중 하나를 생성할 수 있다.

Java 8부터 Random 클래스는 프리미티브 스트림을 생성하기 위한 광범위한 메소드를 제공한다. 예를 들어 다음 코드는 세 가지 요소가 있는 DoubleStream을 만든다.

```java
Random random = new Random();
DoubleStream doubleStream = random.doubles(3);
```

### 8) Stream of String
String 클래스의 `chars()` 메서드를 사용하여 스트림을 생성하기 위한 소스로 String을 사용할 수도 있다. JDK에는 CharStream에 대한 인터페이스가 없으므로 대신 IntStream을 사용하여 문자 스트림을 나타낸다.

```java
IntStream streamOfChars = "abc".chars();
```

다음 예에서는 지정된 RegEx에 따라 String을 하위 문자열로 나눈다.

```java
Stream<String> streamOfString = Pattern.compile(", ").splitAsStream("a, b, c");
```

### 9) Stream of File
Java NIO 클래스 Files를 사용하면 `line()` 메소드를 통해 텍스트 파일의 `Stream<String>`을 생성할 수 있다. 텍스트의 모든 줄은 스트림의 요소가 된다.

```java
Path path = Paths.get("C:\\file.txt");
Stream<String> streamOfStrings = Files.lines(path);
Stream<String> streamWithCharset = Files.lines(path, Charset.forName("UTF-8"));
```

Charset은 `line()` 메서드의 인수로 지정할 수 있다.

## 2. 스트림 참조
중간 작업만 호출되는 한 스트림을 인스턴스화하고 이에 대한 액세스 가능한 참조를 가질 수 있다. 터미널 작업을 실행하면 스트림에 액세스할 수 없게 된다.

```java
Stream<String> stream = Stream.of("a", "b", "c").filter(element -> element.contains("b"));
Optional<String> anyElement = stream.findAny();
```

그러나 터미널 작업을 호출한 후 동일한 참조를 재사용하려고 하면 IllegalStateException이 트리거된다.

```java
Optional<String> firstElement = stream.findFirst();
```

IllegalStateException은 RuntimeException 이므로 컴파일러는 문제에 대해 신호를 보내지 않는다. 따라서 Java 8 스트림은 재사용할 수 없다.

이런 종류의 행동은 논리적이다. 요소를 저장하는 것이 아니라 기능적 스타일로 요소 소스에 유한한 작업 시퀀스를 적용하도록 스트림을 설계했다.

따라서 이전 코드가 제대로 작동하려면 몇 가지 사항을 변경해야 한다.

```java
List<String> elements = Stream.of("a", "b", "c").filter(element -> element.contains("b")).collect(Collectors.toList());
Optional<String> anyElement = elements.stream().findAny();
Optional<String> firstElement = elements.stream().findFirst();
```

## 3. 스트림 파이프라인
데이터 소스의 요소에 대해 일련의 작업을 수행하고 그 결과를 집계하려면 소스, 중간 작업 및 터미널 작업의 세 부분이 필요하다.

중간 작업은 수정된 새 스트림을 반환한다. 예를 들어, 몇 가지 요소 없이 기존 스트림의 새 스트림을 생성하려면 `Skip()` 메서드를 사용해야 한다.

```java
Stream<String> onceModifiedStream = Stream.of("abcd", "bbcd", "cbcd").skip(1);
```

둘 이상의 수정이 필요한 경우 중간 작업을 연결할 수 있다. 현재 `Stream<String>`의 모든 요소를 ​​처음 몇 문자의 하위 문자열로 대체해야 한다고 가정한다. `Skip()` 및 `map()` 메소드를 연결하여 이를 수행할 수 있다.

```java
Stream<String> twiceModifiedStream = stream.skip(1).map(element -> element.substring(0, 3));
```

`map()` 메소드는 람다 표현식을 매개변수로 사용한다.

스트림 자체는 가치가 없다. 사용자는 터미널 작업의 결과에 관심이 있다. 이는 일부 유형의 값이거나 스트림의 모든 요소에 적용되는 작업일 수 있다. 스트림당 하나의 터미널 작업만 사용할 수 있다.

스트림을 사용하는 정확하고 가장 편리한 방법은 스트림 소스, 중간 작업 및 터미널 작업의 체인인 스트림 파이프라인을 사용하는 것이다.

```java
List<String> list = Arrays.asList("abc1", "abc2", "abc3");
long size = list.stream().skip(1).map(element -> element.substring(0, 3)).sorted().count();
```

## 4. 게으른 호출
중간 작업은 게으르다. 즉, 터미널 작업 실행에 필요한 경우에만 호출된다.

예를 들어, 호출될 때마다 내부 카운터를 증가시키는 `wasCalled()` 메서드를 호출해본다.

```java
private long counter;
 
private void wasCalled() {
    counter++;
}
```

이제 `filter()` 작업에서 `wasCalled()` 메서드를 호출해본다.

```java
List<String> list = Arrays.asList(“abc1”, “abc2”, “abc3”);
counter = 0;
Stream<String> stream = list.stream().filter(element -> {
    wasCalled();
    return element.contains("2");
});
```

세 가지 요소의 소스가 있으므로 `filter()` 메서드가 세 번 호출되고 카운터 변수의 값이 3이 될 것이라고 가정할 수 있다. 그러나 이 코드를 실행해도 카운터가 전혀 변경되지 않는다. 여전히 0이므로 `filter()` 메서드는 한 번도 호출되지 않는다. 단말기 동작이 누락된 이유이다.

`map()` 작업과 터미널 작업 `findFirst()`를 추가하여 이 코드를 약간 다시 작성해본다. 또한 로깅을 통해 메소드 호출 순서를 추적하는 기능도 추가할 것이다.

```java
Optional<String> stream = list.stream().filter(element -> {
    log.info("filter() was called");
    return element.contains("2");
}).map(element -> {
    log.info("map() was called");
    return element.toUpperCase();
}).findFirst();
```

결과 로그는 `filter()` 메서드를 두 번, `map()` 메서드를 한 번 호출했음을 보여준다. 이는 파이프라인이 수직으로 실행되기 때문이다. 이 예에서는 스트림의 첫 번째 요소가 필터의 조건자를 충족하지 않았다. 그런 다음 필터를 통과한 두 번째 요소에 대해 `filter()` 메서드를 호출했다. 세 번째 요소에 대해 `filter()`를 호출하지 않고 파이프라인을 통해 `map()` 메서드로 이동했다.

`findFirst()` 작업은 단 하나의 요소만으로 만족된다. 따라서 이 특정 예에서는 지연 호출을 통해 두 개의 메소드 호출 (`filter()`에 대한 호출과 `map()`에 대한 호출)을 피할 수 있었다.

## 5. 집행순서
성능 관점에서 올바른 순서는 스트림 파이프라인에서 작업 연결의 가장 중요한 측면 중 하나이다.

```java
long size = list.stream().map(element -> {
    wasCalled();
    return element.substring(0, 3);
}).skip(2).count();
```

이 코드를 실행하면 카운터 값이 3만큼 증가한다. 즉, 스트림의 `map()` 메서드를 세 번 호출했지만 크기 값은 1이다. 따라서 결과 스트림에는 요소가 하나만 있으며 세 번 중 두 번은 아무 이유 없이 값비싼 `map()` 작업을 실행했다.

`Skip()` 및 `map()` 메소드의 순서를 변경하면 카운터는 하나만 증가한다. 따라서 `map()` 메서드를 한 번만 호출한다.

```java
long size = list.stream().skip(2).map(element -> {
    wasCalled();
    return element.substring(0, 3);
}).count();
```

이로 인해 다음 규칙이 적용된다. 스트림의 크기를 줄이는 중간 작업은 각 요소에 적용되는 작업보다 먼저 배치되어야 한다. 따라서 우리는 스트림 파이프라인의 상단에 `skip()`, `filter()` 및 `distinct()`와 같은 메서드를 유지해야 한다.

## 6. 스트림 감소
API에는 스트림을 유형이나 기본 요소로 집계하는 `count()`, `max()`, `min()` 및 `sum()`과 같은 많은 터미널 작업이 있다. 그러나 이러한 작업은 미리 정의된 구현에 따라 작동한다. 그렇다면 개발자가 Stream의 축소 메커니즘을 사용자 정의해야 하는 경우 이를 수행할 수 있는 방법에는 두 가지가 있는데, `Reduce()` 와 `Collect()` 메서드이다.

### 1) reduce() 메서드
이 방법에는 서명과 반환 유형에 따라 세 가지 변형이 있다. 다음 매개변수를 가질 수 있다.

* identity - accumulator의 초기 값 또는 스트림이 비어 있고 축적할 것이 없는 경우 기본값이다.

* accumulator - 요소 집계 논리를 지정하는 함수이다. accumulator가 모든 축소 단계에 대해 새 값을 생성하므로 새 값의 양은 스트림의 크기와 동일하며 마지막 값만 유용하다. 이는 성능에 그다지 좋지 않다.

* combiner - accumulator의 결과를 집계하는 함수이다. 다른 스레드의 accumulator 결과를 줄이기 위해 병렬 모드에서만 결합자를 호출한다.

이제 실제로 실행되는 세 가지 방법을 확인한다.

```java
OptionalInt reduced = IntStream.range(1, 4).reduce((a, b) -> a + b);
```

감소 = 6(1 + 2 + 3)

```java
int reducedTwoParams = IntStream.range(1, 4).reduce(10, (a, b) -> a + b);
```

ReducedTwoParams = 16(10 + 1 + 2 + 3)

```java
int reducedParams = Stream.of(1, 2, 3)
  .reduce(10, (a, b) -> a + b, (a, b) -> {
     log.info("combiner was called");
     return a + b;
  });
```

결과는 이전 예제(16)와 동일하며 로그인이 없다. 이는 결합자가 호출되지 않았음을 의미한다. 결합기가 작동하려면 스트림이 병렬이어야 한다.

```java
int reducedParallel = Arrays.asList(1, 2, 3).parallelStream()
    .reduce(10, (a, b) -> a + b, (a, b) -> {
       log.info("combiner was called");
       return a + b;
    });
```

여기서의 결과는 다르며(36) 결합자는 두 번 호출되었다. 여기서 축소는 다음 알고리즘에 따라 작동한다. 누산기는 스트림의 모든 요소를 ID에 추가하여 세 번 실행되었다. 이러한 작업은 동시에 수행되고 있다. 결과적으로 (10 + 1 = 11; 10 + 2 = 12; 10 + 3 = 13;)이 된다. 이제 Combiner는 이 세 가지 결과를 병합할 수 있다. 이를 위해서는 두 번의 반복이 필요하다(12 + 13 = 25, 25 + 11 = 36).

### 2) collect() 메서드
스트림 축소는 다른 터미널 작업인 `Collect()` 메서드를 통해 실행될 수도 있다. 축소 메커니즘을 지정하는 Collector 유형의 인수를 허용한다. 가장 일반적인 작업에 대해 이미 생성되고 사전 정의된 수집기가 있다. Collectors 유형을 사용하여 액세스할 수 있다.

```java
List<Product> productList = Arrays.asList(new Product(23, "potatoes"),
  new Product(14, "orange"), new Product(13, "lemon"),
  new Product(23, "bread"), new Product(13, "sugar"));
```

스트림을 컬렉션 (컬렉션, 목록 또는 세트)으로 변환:

```java
List<String> collectorCollection = productList.stream().map(Product::getName).collect(Collectors.toList());
```

문자열로 줄이기:

```java
String listToString = productList.stream().map(Product::getName).collect(Collectors.joining(", ", "[", "]"));
```

`Joiner()` 메서드는 1~3개의 매개변수(구분자, 접두사, 접미사)를 가질 수 있다. `Joiner()` 사용 시 가장 편리한 점은 개발자가 접미사를 적용하고 구분 기호를 적용하지 않기 위해 스트림이 끝에 도달했는지 확인할 필요가 없다는 것이다. 콜렉터가 처리해 준다.

스트림의 모든 숫자 요소의 평균값 처리:

```java
double averagePrice = productList.stream().collect(Collectors.averagingInt(Product::getPrice));
```

스트림의 모든 숫자 요소의 합계를 처리한다.

```java
int summingPrice = productList.stream().collect(Collectors.summingInt(Product::getPrice));
```

`averagingXX()`, `summingXX()` 및 `summarizingXX()` 메서드는 기본 요소(int, long, double) 및 해당 래퍼 클래스(Integer, Long, Double)와 함께 작동할 수 있다. 이러한 방법의 또 다른 강력한 기능은 매핑을 제공하는 것이다. 결과적으로 개발자는 `Collect()` 메서드 이전에 추가 `map()` 작업을 사용할 필요가 없다.

스트림 요소에 대한 통계 정보 수집:

```java
IntSummaryStatistics statistics = productList.stream().collect(Collectors.summarizingInt(Product::getPrice));
```

IntSummaryStatistics 유형의 결과 인스턴스를 사용하면 개발자는 `toString()` 메서드를 적용하여 통계 보고서를 생성할 수 있다. 결과는 이 `IntSummaryStatistics{count=5, sum=86, min=13,average=17,200000, max=23}`에 공통된 문자열이 된다.

또한 `getCount()`, `getSum()`, `getMin()`, `getAverage()` 및 `getMax()` 메소드를 적용하여 이 객체에서 개수, 합계, 최소값 및 평균에 대한 개별 값을 쉽게 추출할 수 있다. 이러한 값은 모두 단일 파이프라인에서 추출할 수 있다.

지정된 기능에 따라 스트림 요소 그룹화:

```java
Map<Integer, List<Product>> collectorMapOfLists = productList.stream().collect(Collectors.groupingBy(Product::getPrice));
```

위의 예에서 스트림은 모든 제품을 가격별로 그룹화하는 Map으로 축소되었다.

일부 조건에 따라 스트림의 요소를 그룹으로 나눈다.

```java
Map<Boolean, List<Product>> mapPartioned = productList.stream().collect(Collectors.partitioningBy(element -> element.getPrice() > 15));
```

추가 변환을 수행하기 위해 컬렉터를 푸시한다.

```java
Set<Product> unmodifiableSet = productList.stream()
  .collect(Collectors.collectingAndThen(Collectors.toSet(),
  Collections::unmodifiableSet));
```

이 특별한 경우 수집기는 스트림을 Set으로 변환한 다음 변경할 수 없는 Set을 생성했다.

맞춤 수집기:

어떤 이유로 사용자 정의 수집기를 만들어야 하는 경우 가장 쉽고 덜 장황한 방법은 Collector 유형의 `of()` 메서드를 사용하는 것이다.

```java
Collector<Product, ?, LinkedList<Product>> toLinkedList =
  Collector.of(LinkedList::new, LinkedList::add, 
    (first, second) -> { 
       first.addAll(second); 
       return first; 
    });

LinkedList<Product> linkedListOfPersons =
  productList.stream().collect(toLinkedList);
```

이 예에서는 Collector의 인스턴스가 `LinkedList<Person>`으로 축소되었다.

## 7. 병렬 스트림
Java 8 이전에는 병렬화가 복잡했다. ExecutorService와 ForkJoin의 등장으로 개발자의 삶이 조금은 단순해졌지만, 특정 실행기를 생성하는 방법, 실행하는 방법 등은 여전히 ​​기억해 둘 가치가 있었다. Java 8에서는 기능적 스타일로 병렬 처리를 수행하는 방법을 도입했다.

API를 사용하면 병렬 모드에서 작업을 수행하는 병렬 스트림을 만들 수 있다. 스트림의 소스가 Collection 또는 array인 경우, `parallelStream()` 메서드를 사용하여 얻을 수 있다.

```java
Stream<Product> streamOfCollection = productList.parallelStream();
boolean isParallel = streamOfCollection.isParallel();
boolean bigPrice = streamOfCollection
  .map(product -> product.getPrice() * 12)
  .anyMatch(price -> price > 200);
```

스트림의 소스가 Collection 또는 array 이외의 다른 것인 경우 `parallel()` 메서드를 사용해야 한다.

```java
IntStream intStreamParallel = IntStream.range(1, 150).parallel();
boolean isParallel = intStreamParallel.isParallel();
```

내부적으로 Stream API는 자동으로 ForkJoin 프레임워크를 사용하여 작업을 병렬로 실행한다. 기본적으로 공통 스레드 풀이 사용되며 (적어도 지금은) 일부 사용자 정의 스레드 풀을 할당할 수 있는 방법이 없다. 이는 사용자 정의 병렬 수집기 세트를 사용하여 극복할 수 있다.

병렬 모드에서 스트림을 사용하는 경우 작업 차단을 한다. 또한 작업을 실행하는데 비슷한 시간이 필요한 경우 병렬 모드를 사용하는 것이 가장 좋다. 한 작업이 다른 작업보다 훨씬 오래 지속되면 전체 앱의 워크플로가 느려질 수 있다.

병렬 모드의 스트림은 `ential()` 메서드를 사용하여 다시 순차 모드로 변환할 수 있다.

```java
IntStream intStreamSequential = intStreamParallel.sequential();
boolean isParallel = intStreamSequential.isParallel();
```

## [출처 및 참고]
* [https://www.baeldung.com/java-8-streams](https://www.baeldung.com/java-8-streams)
