---
title: Java Stream forEach를 중단하는 방법
author: dejavuhyo
date: 2023-11-15 23:25:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-foreach, break-stream, 자바-스트림]
---

## 1. Java 9의 Stream.takeWhile()
문자열 항목의 스트림이 있고 길이가 홀수인 한 해당 요소를 처리하려고 한다고 가정해 본다.

Java 9 `Stream.takeWhile` 메소드를 사용해 본다.

```java
Stream.of("cat", "dog", "elephant", "fox", "rabbit", "duck")
  .takeWhile(n -> n.length() % 2 != 0)
  .forEach(System.out::println);
```

이것을 실행하면 다음과 같은 출력을 얻는다.

```text
cat
dog
```

어떻게 작동하는지 알아보기 위해 for 루프와 break 문을 사용하여 일반 Java의 동등한 코드와 이를 비교해 본다.

```java
List<String> list = asList("cat", "dog", "elephant", "fox", "rabbit", "duck");
for (int i = 0; i < list.size(); i++) {
    String item = list.get(i);
    if (item.length() % 2 == 0) {
        break;
    }
    System.out.println(item);
}
```

보시다시피 takeWhile 메소드를 사용하면 필요한 것을 정확하게 얻을 수 있다.

## 2. 커스텀 Spliterator
`Stream.spliterator`의 데코레이터로 작동할 사용자 정의 Spliterator를 만들어 본다.

먼저 스트림에서 Spliterator를 가져온 다음 이를 CustomSpliterator로 장식 하고 중단 작업을 제어하기 위한 조건자를 제공한다. 마지막으로 CustomSpliterator에서 새 스트림을 생성한다.

```java
public static <T> Stream<T> takeWhile(Stream<T> stream, Predicate<T> predicate) {
    CustomSpliterator<T> customSpliterator = new CustomSpliterator<>(stream.spliterator(), predicate);
    return StreamSupport.stream(customSpliterator, false);
}
```

CustomSpliterator를 생성하는 방법을 살펴본다.

```java
public class CustomSpliterator<T> extends Spliterators.AbstractSpliterator<T> {

    private Spliterator<T> splitr;
    private Predicate<T> predicate;
    private boolean isMatched = true;

    public CustomSpliterator(Spliterator<T> splitr, Predicate<T> predicate) {
        super(splitr.estimateSize(), 0);
        this.splitr = splitr;
        this.predicate = predicate;
    }

    @Override
    public synchronized boolean tryAdvance(Consumer<? super T> consumer) {
        boolean hadNext = splitr.tryAdvance(elem -> {
            if (predicate.test(elem) && isMatched) {
                consumer.accept(elem);
            } else {
                isMatched = false;
            }
        });
        return hadNext && isMatched;
    }
}
```

그럼 tryAdvance 메소드를 살펴본다. 여기서는 사용자 정의 Spliterator가 장식된 Spliterator의 요소를 처리하는 것을 볼 수 있다. 조건자가 일치하고 초기 스트림에 여전히 요소가 있는 한 처리가 완료된다. 조건 중 하나가 false가 되면 Spliterator가 "중단" 되고 스트리밍 작업이 종료된다.

새로운 도우미 메서드를 테스트해 본다.

```java
@Test
public void whenCustomTakeWhileIsCalled_ThenCorrectItemsAreReturned() {
    Stream<String> initialStream = 
      Stream.of("cat", "dog", "elephant", "fox", "rabbit", "duck");

    List<String> result = 
      CustomTakeWhile.takeWhile(initialStream, x -> x.length() % 2 != 0)
        .collect(Collectors.toList());

    assertEquals(asList("cat", "dog"), result);
}
```

보시다시피 조건이 충족된 후 스트림이 중지되었다. 테스트 목적으로 결과를 목록으로 수집했지만 forEach 호출이나 Stream의 다른 기능을 사용할 수도 있다.

## 3. 커스텀 forEach
중단 메커니즘이 포함된 스트림을 제공하는 것이 유용할 수 있지만 forEach 작업에만 집중하는 것이 더 간단할 수 있다.

데코레이터 없이 `Stream.spliterator`를 직접 사용해 본다.

```java
public class CustomForEach {

    public static class Breaker {
        private boolean shouldBreak = false;

        public void stop() {
            shouldBreak = true;
        }

        boolean get() {
            return shouldBreak;
        }
    }

    public static <T> void forEach(Stream<T> stream, BiConsumer<T, Breaker> consumer) {
        Spliterator<T> spliterator = stream.spliterator();
        boolean hadNext = true;
        Breaker breaker = new Breaker();

        while (hadNext && !breaker.get()) {
            hadNext = spliterator.tryAdvance(elem -> {
                consumer.accept(elem, breaker);
            });
        }
    }
}
```

보시다시피, 새로운 사용자 정의 forEach 메소드는 코드에 스트림을 중지하는데 사용할 수 있는 다음 요소와 차단기 객체를 모두 제공하는 BiConsumer를 호출한다.

단위 테스트에서 이것을 시험해 본다.

```java
@Test
public void whenCustomForEachIsCalled_ThenCorrectItemsAreReturned() {
    Stream<String> initialStream = Stream.of("cat", "dog", "elephant", "fox", "rabbit", "duck");
    List<String> result = new ArrayList<>();

    CustomForEach.forEach(initialStream, (elem, breaker) -> {
        if (elem.length() % 2 == 0) {
            breaker.stop();
        } else {
            result.add(elem);
        }
    });

    assertEquals(asList("cat", "dog"), result);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-break-stream-foreach#custom-spliterator](https://www.baeldung.com/java-break-stream-foreach#custom-spliterator)
