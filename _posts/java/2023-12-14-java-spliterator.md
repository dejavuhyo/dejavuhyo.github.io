---
title: Java Spliterator
author: dejavuhyo
date: 2023-12-14 22:00:00 +0900
categories: [Language, Java]
tags: [java-spliterator, spliterator, 자바-스플리터, 스플리터, 자바-분할기]
---

## 1. Spliterator API

### 1) tryAdvance
시퀀스를 단계별로 실행하는데 사용되는 주요 방법이다. 이 메소드는 Spliterator의 요소를 하나씩 순차적으로 소비하는데 사용되는 소비자를 취하고, 순회할 요소가 없으면 false를 반환한다.

여기서는 요소를 순회하고 분할하는데 이를 사용하는 방법을 살펴본다.

먼저 35000개의 기사가 포함된 ArrayList가 있고 Article 클래스가 다음과 같이 정의되어 있다고 가정해 본다.

```java
public class Article {
    private List<Author> listOfAuthors;
    private int id;
    private String name;
    
    // standard constructors/getters/setters
}
```

이제 Spliterator를 사용하여 기사 목록을 처리하고 각 기사 이름에 "–Published by Baeldung"이라는 접미사를 추가해 본다.

```java
@Test
public void givenAStreamOfArticles_whenProcessedInSequentiallyWithSpliterator_ProducessRightOutput() {
  // ...
}
```

먼저 기사를 생성해 본다.

```java
public void givenAStreamOfArticles_whenProcessedInSequentiallyWithSpliterator_ProducessRightOutput() {
    List<Article> articles = Stream.generate(() -> new Article("Java"))
        .limit(35000)
        .collect(Collectors.toList());

    // ...
}
```

Stream을 사용하여 35000개의 기사를 생성했다. 다음으로, 이 기사 목록 에서 분할자를 만들고 tryAdvance 메서드를 사용하여 기사를 처리해 본다.

```java
Spliterator<Article> spliterator = articles.spliterator();
while (spliterator.tryAdvance(article -> article.setName(article.getName()
    .concat("- published by Baeldung"))));
```

소비자는 기사 이름에 접미사를 추가하는 간단한 함수이다.

마지막으로 모든 기사가 처리되었고 이름이 업데이트되었는지 확인하기 위해 어설션을 수행할 수 있다.

```java
articles.forEach(article -> assertThat(article.getName()).isEqualTo("Java- published by Baeldung"));
```

이 테스트 케이스는 성공적으로 실행될 것이다. 모든 기사 이름은 이미 업데이트되었으며 새 이름은 Baeldung에서 발행한 Java와 동일하다.

또 다른 핵심은 다음 요소를 처리하기 위해 `tryAdvance()` 메서드를 사용했다는 것이다.

### 2) trySplit
Spliterator를 분할하고 파티션을 독립적으로 처리해 본다.

trySplit 메서드는 이를 두 부분으로 분할하려고 시도한다. 그런 다음 호출자는 요소를 처리하고 마지막으로 반환된 인스턴스는 다른 인스턴스를 처리하여 두 인스턴스가 병렬로 처리될 수 있도록 한다.

이전과 마찬가지로 기사와 분할기를 생성한다.

```java
@Test
public void givenAStreamOfArticle_whenProcessedUsingTrySplit_thenSplitIntoEqualHalf() {
    List<Article> articles = Stream.generate(() -> new Article("Java"))
        .limit(35000)
        .collect(Collectors.toList());

    Spliterator<Article> split1 = articles.spliterator();
    
    // ...
}
```

그런 다음 첫 번째 분할자에 trySplit 메서드를 적용하여 두 번째 분할자를 만든다.

```java
Spliterator<Article> split2 = split1.trySplit(); 
```

위 코드에서는 `split1.trySplit()` 35000개의 기사를 split1 동일한 크기의 두 부분으로 분할하려고 시도한다. 원래 분할기의 후반부를 나타내는 새 분할기를 반환하고 이를 split2에 할당한다.

이제 이 두 분할을 사용하는 예를 확인해 본다. 이러한 분할기에 의해 처리된 결과를 저장할 두 개의 목록을 만들어 본다.

```java
List<Article> articlesListOne = new ArrayList<>(); 
List<Article> articlesListTwo = new ArrayList<>();
```

기사를 소비해 본다.

```java
split1.forEachRemaining(articlesListOne::add);
split2.forEachRemaining(articlesListTwo::add);
```

목록을 생성한 후 split1을 반복하고 split1의 모든 기사를 articlesListOne에 추가한다. 마찬가지로, Split2에 대해서도 동일한 작업을 수행하여 Split2의 각 기사를 ArticleListTwo에 저장한다.

다음으로, 이러한 분할기가 물품의 정확히 절반, 즉 17500개를 소비했다고 주장할 수 있다.

```java
assertThat(articlesListOne.size()).isEqualTo(17500);
assertThat(articlesListTwo.size()).isEqualTo(17500);
```

마지막으로 두 목록에 서로 다른 요소가 포함되어 있는지 확인하는 어설션을 만들 수 있다.

```java
assertThat(articlesLitOne).doesNotContainAnyElementsOf(articlesListTwo);
```

이 테스트 케이스는 성공적으로 실행될 것이다. 기사 SplitOne에 있는 기사는 기사 SplitTwo에 존재하지 않는다. 이는 파티션을 독립적으로 처리할 수 있다는 결론을 내린다.

분할 프로세스는 의도한 대로 작동했으며 기록을 균등하게 분할했다.

### 3) estimatedSize
estimateSize 메소드는 추정된 요소 수를 제공한다.

```java
log.info("Size: " + split1.estimateSize());
```

그러면 다음이 출력된다.

```text
Size: 17500
```

### 4) hasCharacteristics
이 API는 주어진 특성이 Spliterator의 속성과 일치하는지 확인한다. 그런 다음 위의 메서드를 호출하면 출력은 해당 특성을 int로 표현한 것이다.

```java
log.info("Characteristics: " + split1.characteristics());
```

```text
Characteristics: 16464
```

## 2. Spliterator 특성
행동을 설명하는 8가지 다른 특성을 가지고 있다. 이는 외부 도구에 대한 힌트로 사용할 수 있다.

* **SIZED** - `estimateSize()` 메소드를사용하여 정확한 수의 요소를 반환할 수 있는 경우

* **SORTED** - 정렬된 소스를 반복하는 경우

* **SUBSIZED** - `trySplit()` 메소드를 사용하여 인스턴스를 분할 하고 SIZED인 Spliterator를 얻는경우

* **CONCURRENT** - 소스를 동시에 안전하게 수정할 수 있는 경우

* **DISTINCT** - 발견된 각 요소 쌍에 대해 `x`, `y`, `!x.equals(y)`

* **IMMUTABLE** - 소스에 포함된 요소를 구조적으로 수정할 수 없는 경우

* **NONNULL** - 소스가 null을 보유하는지 여부

* **ORDERED** - 순서가 지정된 시퀀스를 반복하는 경우

## 3. Spliterator 커스텀

### 1) 맞춤설정해야 하는 경우
이 예에서는 사용자 정의 Splitter를 작성하는 방법을 이해하기 위한 쉬운 예를 제시한다. 사용자 정의 스플리터를 사용하면 소스의 모든 요소를 ​​하나씩 횡단할 수 있다. 이 배열의 유형은 배열의 사용자 정의 모델 객체, IO 채널 또는 생성기 함수일 수 있다.

### 2) 사용자 정의 방법
사용자 정의 분할기를 사용하여 큰 정수 배열에 있는 모든 요소의 합계를 계산한다고 가정해 본다. 이를 해결하려면 Integer 목록을 하위 목록으로 분할하는 Spliterator를 구현 해야 한다. 사용자 정의 Spliterator의 구현은 다음과 같다.

```java
public class CustomSpliterator implements Spliterator<Integer> {
    private final List<Integer> elements;
    private int currentIndex;
    
    public CustomSpliterator(List<Integer> elements) {
        this.elements = elements;
        this.currentIndex = 0;
    }
    
    @Override
    public boolean tryAdvance(Consumer<? super Integer> action) {
        if (currentIndex < elements.size()) {
            action.accept(elements.get(currentIndex));
            currentIndex++;
            return true;
        }
        return false;
    }
    
    @Override
    public Spliterator<Integer> trySplit() {
        int currentSize = elements.size() - currentIndex;
        if (currentSize < 2) {
            return null;
        }
        
        int splitIndex = currentIndex + currentSize / 2;
        CustomSpliterator newSpliterator = new CustomSpliterator(elements.subList(currentIndex, splitIndex));
        currentIndex = splitIndex;
        return newSpliterator;
    }
    
    @Override
    public long estimateSize() {
        return elements.size() - currentIndex;
    }
    
    @Override
    public int characteristics() {
        return ORDERED | SIZED | SUBSIZED | NONNULL;
    }
}
```

컬렉션을 순차적으로 처리하는 CustomSpliterator 테스트이다.

```java
@Test
public void givenAStreamOfIntegers_whenProcessedSequentialCustomSpliterator_countProducesRightOutput() {
        List<Integer> numbers = new ArrayList<>();
        numbers.add(1);
        numbers.add(2);
        numbers.add(3);
        numbers.add(4);
        numbers.add(5);

        CustomSpliterator customSpliterator = new CustomSpliterator(numbers);

        AtomicInteger sum = new AtomicInteger();

        customSpliterator.forEachRemaining(sum::addAndGet);
        assertThat(sum.get()).isEqualTo(15);
}
```

컬렉션을 병렬로 처리하는 CustomSpliterator 테스트이다.

```java
@Test
public void givenAStreamOfIntegers_whenProcessedInParallelWithCustomSpliterator_countProducesRightOutput() {
        List<Integer> numbers = new ArrayList<>();
        numbers.add(1);
        numbers.add(2);
        numbers.add(3);
        numbers.add(4);
        numbers.add(5);

        CustomSpliterator customSpliterator = new CustomSpliterator(numbers);

        // Create a ForkJoinPool for parallel processing
        ForkJoinPool forkJoinPool = ForkJoinPool.commonPool();

        AtomicInteger sum = new AtomicInteger(0);

        // Process elements in parallel using parallelStream
        forkJoinPool.submit(() -> customSpliterator.forEachRemaining(sum::addAndGet)).join();
        assertThat(sum.get()).isEqualTo(15);
}
```

병렬 처리를 활용하면 요소가 여러 부분으로 분할되어 동시에 처리되므로 대규모 데이터 세트 또는 계산 집약적인 작업의 성능이 잠재적으로 향상된다.

또한 사용자 정의 Spliterator는 정수 목록에서 생성되고 현재 위치를 유지하여 이를 통과한다.

각 메소드의 구현에 대해 더 자세히 논의해 본다.

* CustomSpliterator는 생성자에서 정수 목록을 가져와 현재 처리 중인 인덱스를 추적한다.

* `tryAdvance()` 메서드는 사용 가능한 다음 요소를 사용하고 다음 요소가 존재하고 현재 인덱스를 진행하는 경우 true를 반환하도록 구현된다. 더 이상 요소가 없으면 false를 반환한다.

* `trySplit()` 메서드는 나머지 요소를 두 부분으로 분할한다. 현재 인덱스부터 분할 인덱스까지의 하위 목록을 사용하여 새로운 CustomSpliterator를 생성한다. 남은 크기가 분할하기에 너무 작은 경우 `trySplit()`은 null을 반환한다.

* `estimateSize()` 메서드는 처리할 남은 요소 수의 추정치를 반환한다.

* `characteristics()` 메서드는 Spliterator의 특성을 지정한다. 이 경우 ORDERED, SIZED, SUBSIZED 및 NONNULL 특성이 설정된다.


## 4. Primitive Values 지원
Spliterator API는 double, int 및 long을 포함한 기본 값을 지원한다.

일반 Spliterator와 기본 전용 Spliterator를 사용하는 것의 유일한 차이점은 주어진 Consumer와 Spliterator의 유형 이다.

예를 들어 int 값에 필요한 경우 intConsumer를 전달해야 한다. 또한 기본 전용 Spliterator 목록은 다음과 같다.

* `OfPrimitive<T, T_CONS, T_SPLITR extends Spliterator.OfPrimitive<T, T_CONS, T_SPLITR>>`: 다른 기본 요소에 대한 상위 인터페이스

* `OfInt`: int에 특화된 Spliterator

* `OfDouble`: double 전용 Spliterator

* `OfLong`: long 전용 Spliterator

## [출처 및 참고]
* [https://www.baeldung.com/java-spliterator](https://www.baeldung.com/java-spliterator)
