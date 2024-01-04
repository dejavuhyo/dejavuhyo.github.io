---
title: Java 스트림 Stream.reduce()
author: dejavuhyo
date: 2024-01-04 09:00:00 +0900
categories: [Language, Java]
tags: [java-streams, streams-reduce, reduce, 자바-스트림, 스트림-리듀스]
---

## 1. Identity, Accumulator 그리고 Combiner

* Identity - 축소 작업의 초기 값이자 스트림이 비어 있는 경우 기본 결과인 요소이다.

* Accumulator - 축소 작업의 부분 결과와 스트림의 다음 요소라는 두 가지 매개변수를 취하는 함수이다.

* Combiner  - 축소가 병렬화되거나 accumulator 인수 유형과 accumulator 구현 유형이 일치하지 않을때 축소 작업의 부분 결과를 결합하는데 사용되는 함수이다.

## 2. Stream.reduce() 사용
몇 가지 기본 예이다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
int result = numbers
  .stream()
  .reduce(0, (subtotal, element) -> subtotal + element);
assertThat(result).isEqualTo(21);
```

이 경우 정수 값 0이 ID이다. 축소 작업의 초기 값과 정수 값 스트림이 비어 있을 때의 기본 결과도 저장한다.

마찬가지로 람다 표현식은 다음과 같다.

```java
subtotal, element -> subtotal + element
```

정수 값과 스트림의 다음 요소의 부분 합을 취하므로 accumulator이다.

코드를 더욱 간결하게 만들기 위해 람다식 대신 메서드 참조를 사용할 수 있다.

```java
int result = numbers.stream().reduce(0, Integer::sum);
assertThat(result).isEqualTo(21);
```

물론, 다른 유형의 요소를 보유하는 스트림에 대해 `Reduce()` 작업을 사용할 수 있다.

예를 들어, String 요소의 배열에 대해 `Reduce()`를 사용하고 이를 단일 결과로 결합할 수 있다.

```java
List<String> letters = Arrays.asList("a", "b", "c", "d", "e");
String result = letters
  .stream()
  .reduce("", (partialString, element) -> partialString + element);
assertThat(result).isEqualTo("abcde");
```

마찬가지로 메소드 참조를 사용하는 버전으로 전환할 수 있다.

```java
String result = letters.stream().reduce("", String::concat);
assertThat(result).isEqualTo("abcde");
```

문자 배열의 대문자 요소를 결합하기 위해 `Reduce()` 작업을 사용한다.

```java
String result = letters
  .stream()
  .reduce(
    "", (partialString, element) -> partialString.toUpperCase() + element.toUpperCase());
assertThat(result).isEqualTo("ABCDE");
```

또한 병렬화된 스트림에서 `Reduce()`를 사용할 수 있다.

```java
List<Integer> ages = Arrays.asList(25, 30, 45, 28, 32);
int computedAges = ages.parallelStream().reduce(0, (a, b) -> a + b, Integer::sum);
```

스트림이 병렬로 실행되면 Java 런타임은 스트림을 여러 하위 스트림으로 분할한다. 이러한 경우 하위 스트림의 결과를 단일 결과로 결합하는 함수를 사용해야 한다. 이것이 결합자의 역할이다. 위의 코드 조각에서는 `Integer::sum` 메서드 참조이다.

이 코드는 컴파일되지 않는다.

```java
List<User> users = Arrays.asList(new User("John", 30), new User("Julie", 35));
int computedAges = users.stream().reduce(0, (partialAgeResult, user) -> partialAgeResult + user.getAge());
```

이 경우 User 개체 스트림이 있고 accumulator 인수의 유형은 Integer 및 User이다. 그러나 accumulator 구현은 정수의 합이므로 컴파일러는 사용자 매개변수의 유형을 추론할 수 없다.

combiner를 사용하여 이 문제를 해결할 수 있다.

```java
int result = users.stream()
  .reduce(0, (partialAgeResult, user) -> partialAgeResult + user.getAge(), Integer::sum);
assertThat(result).isEqualTo(65);
```

간단히 말해서 순차 스트림과 accumulator 인수 유형 및 구현 유형이 일치하면 결합자를 사용할 필요가 없다.

## 3. 병렬로 줄이기
이전에 배운 것처럼 병렬화된 스트림에서 `Reduce()`를 사용할 수 있다.

병렬화된 스트림을 사용하는 경우 스트림에서 실행되는 `Reduce()` 또는 기타 집계 작업이 다음과 같은지 확인해야 한다.

* associative: 결과는 피연산자의 순서에 영향을 받지 않는다.
 
* non-interfering: 작업이 데이터 소스에 영향을 주지 않는다.

* stateless and deterministic: 작업에 상태가 없으며 주어진 입력에 대해 동일한 출력을 생성한다.
* 
예측할 수 없는 결과를 방지하려면 이러한 조건을 모두 충족해야 한다.

예상대로, `Reduce()`를 포함하여 병렬화된 스트림에서 수행되는 작업은 병렬로 실행되므로 멀티 코어 하드웨어 아키텍처를 활용한다.

분명한 이유로 병렬화된 스트림은 순차 스트림보다 훨씬 더 성능이 좋다. 그렇더라도 스트림에 적용되는 작업이 비싸지 않거나 스트림의 요소 수가 적은 경우에는 과잉일 수 있다.

물론, 병렬화된 스트림은 대규모 스트림으로 작업하고 비용이 많이 드는 집계 작업을 수행해야 할 때 사용하는 올바른 방법이다.

간단한 JMH(Java Microbenchmark Harness) 벤치마크 테스트를 만들고 순차 및 병렬 스트림에서 `Reduce()` 작업을 사용할 때 각각의 실행 시간을 비교해 본다.

```java
@State(Scope.Thread)
private final List<User> userList = createUsers();

@Benchmark
public Integer executeReduceOnParallelizedStream() {
    return this.userList
      .parallelStream()
      .reduce(
        0, (partialAgeResult, user) -> partialAgeResult + user.getAge(), Integer::sum);
}

@Benchmark
public Integer executeReduceOnSequentialStream() {
    return this.userList
      .stream()
      .reduce(
        0, (partialAgeResult, user) -> partialAgeResult + user.getAge(), Integer::sum);
}
```

위의 JMH 벤치마크에서는 실행 평균 시간을 비교한다. 단순히 많은 수의 사용자 개체를 포함하는 목록을 만든다. 다음으로 순차 및 병렬 스트림에 대해 `Reduce()`를 호출하고 후자가 전자보다 더 빠르게 수행되는지 확인한다(작업당 초 단위).

벤치마크 결과는 다음과 같다.

```text
Benchmark                                                   Mode  Cnt  Score    Error  Units
JMHStreamReduceBenchMark.executeReduceOnParallelizedStream  avgt    5  0,007 ±  0,001   s/op
JMHStreamReduceBenchMark.executeReduceOnSequentialStream    avgt    5  0,010 ±  0,001   s/op
```

## 4. 축소하는 동안 예외 발생 및 처리
위의 예에서 `Reduce()` 작업은 예외를 발생시키지 않는다. 하지만 물론 그럴 수도 있다.

예를 들어, 스트림의 모든 요소를 ​​제공된 요소로 나눈 다음 합산해야 한다고 가정해 본다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
int divider = 2;
int result = numbers.stream().reduce(0, a / divider + b / divider);
```

구분선 변수가 0이 아닌한 작동한다. 그러나 0이면, `Reduce()`는 ArithmeticException 예외를 발생시킨다 : divide by zero.

예외를 쉽게 포착하고 `try/catch` 블록을 사용하여 사용 사례에 따라 예외 기록, 복구 등 유용한 작업을 수행할 수 있다.

```java
public static int divideListElements(List<Integer> values, int divider) {
    return values.stream()
      .reduce(0, (a, b) -> {
          try {
              return a / divider + b / divider;
          } catch (ArithmeticException e) {
              LOGGER.log(Level.INFO, "Arithmetic Exception: Division by Zero");
          }
          return 0;
      });
}
```

이 접근 방식은 작동하지만 `try/catch` 블록으로 람다식을 오염시켰다. 더 이상 이전에 가졌던 깔끔한 단일 라이너가 없다.

이 문제를 해결하려면 추출 함수 리팩토링 기술을 사용하고 `try/catch` 블록을 별도의 메서드로 추출 할 수 있다.

```java
private static int divide(int value, int factor) {
    int result = 0;
    try {
        result = value / factor;
    } catch (ArithmeticException e) {
        LOGGER.log(Level.INFO, "Arithmetic Exception: Division by Zero");
    }
    return result
}
```

이제 `DivideListElements()` 메서드의 구현이 다시 깔끔하고 간소화되었다.

```java
public static int divideListElements(List<Integer> values, int divider) {
    return values.stream().reduce(0, (a, b) -> divide(a, divider) + divide(b, divider));
}
```

`DivideListElements()`가 추상 NumberUtils 클래스에 의해 구현된 유틸리티 메서드라고 가정하면, `DivideListElements()` 메서드의 동작을 확인하기 위한 단위 테스트를 만들 수 있다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
assertThat(NumberUtils.divideListElements(numbers, 1)).isEqualTo(21);
```

또한 제공된 정수 값 목록에 0이 포함된 경우 `DivideListElements()` 메서드를 테스트해 본다.

```java
List<Integer> numbers = Arrays.asList(0, 1, 2, 3, 4, 5, 6);
assertThat(NumberUtils.divideListElements(numbers, 1)).isEqualTo(21);
```

마지막으로 나누기가 0일 때 메서드 구현을 테스트해 본다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
assertThat(NumberUtils.divideListElements(numbers, 0)).isEqualTo(0);
```

## 5. 복잡한 사용자 정의 개체
기본이 아닌 필드를 포함하는 사용자 정의 개체와 함께 `Stream.reduce()`를 사용할 수도 있다. 그렇게 하려면 데이터 유형에 대한 관련 identity, accumulator 및 combiner를 제공해야 한다.

사용자가 리뷰 웹사이트의 일부라고 가정한다. 각 사용자는 하나의 평가를 가질 수 있으며 이는 여러 리뷰에 대한 평균이다.

먼저 Review 개체부터 시작한다.

각 리뷰에는 간단한 의견과 점수가 포함되어야 한다.

```java
public class Review {

    private int points;
    private String review;

    // constructor, getters and setters
}
```

다음으로, 포인트 필드와 함께 리뷰를 보관할 등급을 정의해야 한다. 더 많은 리뷰를 추가하면 이 필드는 그에 따라 증가하거나 감소한다.

```java
public class Rating {

    double points;
    List<Review> reviews = new ArrayList<>();

    public void add(Review review) {
        reviews.add(review);
        computeRating();
    }

    private double computeRating() {
        double totalPoints = 
          reviews.stream().map(Review::getPoints).reduce(0, Integer::sum);
        this.points = totalPoints / reviews.size();
        return this.points;
    }

    public static Rating average(Rating r1, Rating r2) {
        Rating combined = new Rating();
        combined.reviews = new ArrayList<>(r1.reviews);
        combined.reviews.addAll(r2.reviews);
        combined.computeRating();
        return combined;
    }

}
```

또한 두 개의 입력 Rating을 기반으로 평균을 계산하는 평균 함수를 추가했다. 이는 combiner 및 accumulator 구성 요소에 잘 작동한다.

다음으로 각각 고유한 리뷰 세트가 있는 User 목록을 정의한다.

```java
User john = new User("John", 30);
john.getRating().add(new Review(5, ""));
john.getRating().add(new Review(3, "not bad"));
User julie = new User("Julie", 35);
john.getRating().add(new Review(4, "great!"));
john.getRating().add(new Review(2, "terrible experience"));
john.getRating().add(new Review(4, ""));
List<User> users = Arrays.asList(john, julie);
```

이제 John과 Julie가 고려되었으므로 `Stream.reduce()`를 사용하여 두 사용자의 평균 평점을 계산해 본다.

신원으로서 입력 목록이 비어 있으면 새 Rating을 반환한다.

```java
Rating averageRating = users.stream()
  .reduce(new Rating(), 
    (rating, user) -> Rating.average(rating, user.getRating()), 
    Rating::average);
```

수학적으로 계산하면 평균 점수가 3.6이라는 것을 알 수 있다.

```java
assertThat(averageRating.getPoints()).isEqualTo(3.6);
```

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-reduce](https://www.baeldung.com/java-stream-reduce)
