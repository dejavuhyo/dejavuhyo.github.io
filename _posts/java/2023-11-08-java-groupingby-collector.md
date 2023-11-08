---
title: Java 8 groupingBy Collector
author: dejavuhyo
date: 2023-11-08 22:00:00 +0900
categories: [Language, Java]
tags: [java-groupingby, groupingby, groupingby-collector, 자바-수집기]
---

## 1. groupingBy Collectors
Java 8 Stream API를 사용하면 선언적인 방식으로 데이터 컬렉션을 처리할 수 있다.

정적 팩토리 메소드 `Collectors.groupingBy()` 및 `Collectors.groupingByConcurrent()`는 SQL 언어의 'GROUP BY' 절과 유사한 기능을 제공한다. 일부 속성을 기준으로 객체를 그룹화하고 결과를 Map 인스턴스에 저장하는데 사용한다.

groupingBy의 오버로드된 메서드는 다음과 같다.

* 먼저 분류 함수를 메소드 매개변수로 사용한다.

```java
static <T,K> Collector<T,?,Map<K,List<T>>> groupingBy(Function<? super T,? extends K> classifier)
```

* 둘째, 분류 함수와 두 번째 수집기를 메서드 매개변수로 사용한다.

```java
static <T,K,A,D> Collector<T,?,Map<K,D>> groupingBy(Function<? super T,? extends K> classifier, Collector<? super T,A,D> downstream)
```

* 마지막으로 분류 함수, 공급자 메서드(최종 결과를 포함하는 Map 구현 제공) 및 메서드 매개변수인 두 번째 수집기를 사용한다.

```java
static <T,K,D,A,M extends Map<K,D>> Collector<T,?,M>  groupingBy(Function<? super T,? extends K> classifier, Supplier<M> mapFactory, Collector<? super T,A,D> downstream)
```

### 1) 예제 코드 설정
`groupingBy()`의 사용법을 보여주기 위해 BlogPost 클래스를 정의해 본다 (BlogPost 개체 스트림을 사용한다).

```java
class BlogPost {
    String title;
    String author;
    BlogPostType type;
    int likes;
}
```

다음으로 BlogPostType은 다음과 같다.

```java
enum BlogPostType {
    NEWS,
    REVIEW,
    GUIDE
}
```

그런 다음 BlogPost 개체 목록이다.

```java
List<BlogPost> posts = Arrays.asList( ... );
```

유형과 작성자 속성의 조합에 따라 게시물을 그룹화하는데 사용되는 Tuple 클래스도 정의해 본다.

```java
class Tuple {
    BlogPostType type;
    String author;
}
```

### 2) 단일 열로 단순 그룹화
분류 함수만 매개변수로 사용하는 가장 간단한 groupingBy 메서드 부터 시작해 본다. 분류 함수는 스트림의 각 요소에 적용된다.

함수에서 반환된 값을 groupingBy 수집기에서 가져온 맵의 키로 사용한다.

블로그 게시물 목록에서 블로그 게시물을 유형별로 그룹화하려면 다음을 수행 한다.

```java
Map<BlogPostType, List<BlogPost>> postsPerType = posts.stream().collect(groupingBy(BlogPost::getType));
```

### 3) 복잡한 맵 키 유형을 사용한 groupingBy
분류 함수는 스칼라 또는 문자열 값만 반환하는 것으로 제한되지 않는다. 결과 맵의 키는 필요한 equals 및 hashcode 메소드를 구현하는 한 모든 객체가 될 수 있다.

두 개의 필드를 키로 사용하여 그룹화 하려면 `javafx.util` 또는 `org.apache.commons.lang3.tuple` 패키지에 제공된 pair 클래스를 사용할 수 있다.

예를 들어 Apache Commons 쌍 인스턴스에 결합된 유형 및 작성자별로 목록의 블로그 게시물을 그룹화하려면 다음을 수행한다.

```java
Map<Pair<BlogPostType, String>, List<BlogPost>> postsPerTypeAndAuthor = posts.stream().collect(groupingBy(post -> new ImmutablePair<>(post.getType(), post.getAuthor())));
```

마찬가지로 이전에 정의한 Tuple 클래스를 사용할 수 있다. 이 클래스는 필요에 따라 더 많은 필드를 포함하도록 쉽게 일반화할 수 있다. Tuple 인스턴스를 사용한 이전 예는 다음과 같다.

```java
Map<Tuple, List<BlogPost>> postsPerTypeAndAuthor = posts.stream().collect(groupingBy(post -> new Tuple(post.getType(), post.getAuthor())));
```

Java 16에서는 불변 Java 클래스를 생성하는 새로운 형태로 레코드 개념을 도입했다.

기록 기능은 Tuple보다 groupingBy를 수행하는 더 간단하고 명확하며 안전한 방법을 제공한다. 예를 들어 BlogPost에 레코드 인스턴스를 정의했다.

```java
public class BlogPost {
    private String title;
    private String author;
    private BlogPostType type;
    private int likes;
    record AuthPostTypesLikes(String author, BlogPostType type, int likes) {};
    
    // constructor, getters/setters
}
```

이제 레코드 인스턴스를 사용하여 유형, 작성자 및 좋아요별로 목록에서 BlotPost를 그룹화하는 것이 매우 간단하다.

```java
Map<BlogPost.AuthPostTypesLikes, List<BlogPost>> postsPerTypeAndAuthor = posts.stream().collect(groupingBy(post -> new BlogPost.AuthPostTypesLikes(post.getAuthor(), post.getType(), post.getLikes())));
```

### 4) 반환된 맵 값 유형 수정
groupingBy의 두 번째 오버로드는 첫 번째 수집기의 결과에 적용되는 추가 두 번째 수집기(다운스트림 수집기)를 사용한다.

분류 함수를 지정하지만 다운스트림 수집기를 지정하지 않으면 `toList()` 수집기가 뒤에서 사용된다.

`toSet()` 컬렉터를 다운스트림 컬렉터로 사용 하고 (List 대신) 블로그 게시물 세트를 가져온다.

```java
Map<BlogPostType, Set<BlogPost>> postsPerType = posts.stream().collect(groupingBy(BlogPost::getType, toSet()));
```

### 5) 여러 필드로 그룹화
다운스트림 수집기의 다른 적용은 첫 번째 그룹화 기준의 결과에 대해 보조 groupingBy를 수행하는 것이다.

BlogPost 목록을 먼저 작성자 별로 그룹화 한 다음 유형별로 그룹화하려면 다음을 수행한다.

```java
Map<String, Map<BlogPostType, List>> map = posts.stream().collect(groupingBy(BlogPost::getAuthor, groupingBy(BlogPost::getType)));
```

### 6) 그룹화된 결과에서 평균 얻기
다운스트림 수집기를 사용하면 분류 함수 결과에 집계 함수를 적용할 수 있다.

예를 들어, 각 블로그 게시물 유형에 대한 평균 좋아요 수를 찾으려면 다음을 수행한다.

```java
Map<BlogPostType, Double> averageLikesPerType = posts.stream().collect(groupingBy(BlogPost::getType, averagingInt(BlogPost::getLikes)));
```

### 7) 그룹화된 결과에서 합계 얻기
각 유형 에 대한 총 좋아요 수를 계산하려면 다음을 수행한다.

```java
Map<BlogPostType, Integer> likesPerType = posts.stream().collect(groupingBy(BlogPost::getType, summingInt(BlogPost::getLikes)));
```

### 8) 그룹화된 결과에서 최대값 또는 최소값 얻기
수행할 수 있는 또 다른 집계는 좋아요 수가 최대인 블로그 게시물을 얻는 것이다.

```java
Map<BlogPostType, Optional<BlogPost>> maxLikesPerPostType = posts.stream().collect(groupingBy(BlogPost::getType, maxBy(comparingInt(BlogPost::getLikes))));
```

마찬가지로 minBy 다운스트림 컬렉터를 적용하여 좋아요 수가 최소인 블로그 게시물을 얻을 수 있다.

maxBy 및 minBy 수집기는 자신이 적용되는 컬렉션이 비어 있을 가능성을 고려한다. 이것이 맵의 값 유형이 `Optional<BlogPost>`인 이유이다.

### 9) 그룹화된 결과의 속성에 대한 요약 가져오기
Collectors API는 숫자 속성의 개수, 합계, 최소값, 최대 값 및 평균을 동시에 계산해야 하는 경우에 사용할 수 있는 요약 수집기를 제공한다.

각 유형에 대한 블로그 게시물의 좋아요 속성에 대한 요약을 계산해 본다.

```java
Map<BlogPostType, IntSummaryStatistics> likeStatisticsPerType = posts.stream().collect(groupingBy(BlogPost::getType, summarizingInt(BlogPost::getLikes)));
```

각 유형의 IntSummaryStatistics 개체에는 좋아요 속성에 대한 개수, 합계, 평균, 최소값 및 최대값이 포함되어 있다. double 및 long 값에 대한 추가 요약 개체가 있다.

### 10) 그룹화된 결과의 여러 속성 집계
이전에는 한 번에 하나의 필드를 집계하는 방법이었다. 여러 필드에 대한 집계를 수행하기 위해 따를 수 있는 몇 가지 기술이 있다.

첫 번째 접근 방식은 groupingBy의 다운스트림 수집기에 대해 `Collectors::collectingAndThen`을 사용하는 것이다. collectionAndThen의 첫 번째 매개변수의 경우 `Collectors::toList`를 사용하여 스트림을 목록으로 수집한다. 두 번째 매개변수는 마무리 변환을 적용한다. 이를 집계를 지원하는 수집기의 클래스 메서드와 함께 사용하여 원하는 결과를 얻을 수 있다.

예를 들어 작성자 별로 그룹화 하고 각 작성자에 대해 제목 수를 계산하고 제목을 나열 하고 좋아요에 대한 요약 통계를 제공한다. 이를 달성하기 위해 BlogPost에 새 레코드를 추가하는 것부터 시작한다.

```java
public class BlogPost {
    // ...
    record PostCountTitlesLikesStats(long postCount, String titles, IntSummaryStatistics likesStats){};
     // ...
}
```

groupingBy 및 collectingAndThen의 구현은 다음과 같다.

```java
Map<String, BlogPost.PostCountTitlesLikesStats> postsPerAuthor = posts.stream()
  .collect(groupingBy(BlogPost::getAuthor, collectingAndThen(toList(), list -> {
    long count = list.stream()
      .map(BlogPost::getTitle)
      .collect(counting());
    String titles = list.stream()
      .map(BlogPost::getTitle)
      .collect(joining(" : "));
    IntSummaryStatistics summary = list.stream()
      .collect(summarizingInt(BlogPost::getLikes));
    return new BlogPost.PostCountTitlesLikesStats(count, titles, summary);
  })));
```

collectionAndThen의 첫 번째 매개변수에서 BlogPost 목록을 얻는다. PostCountTitlesLikesStats를 생성하기 위한 값을 계산하기 위해 마무리 변환에서 이를 람다 함수에 대한 입력으로 사용한다.

특정 작성자에 대한 정보를 얻는 방법은 다음과 같이 간단하다.

```java
BlogPost.PostCountTitlesLikesStats result = postsPerAuthor.get("Author 1");
assertThat(result.postCount()).isEqualTo(3L);
assertThat(result.titles()).isEqualTo("News item 1 : Programming guide : Tech review 2");
assertThat(result.likesStats().getMax()).isEqualTo(20);
assertThat(result.likesStats().getMin()).isEqualTo(15);
assertThat(result.likesStats().getAverage()).isEqualTo(16.666d, offset(0.001d));
```

`Collectors::toMap`을 사용하여 스트림 요소를 수집하고 집계하면 더욱 정교한 집계를 수행할 수도 있다.

BlogPost 요소를 작성자 별로 그룹화 하고 제목을 유사한 점수의 상한 합계와 연결 하려는 간단한 예를 고려해 본다.

먼저, 집계된 결과를 캡슐화할 레코드를 만든다.

```java
public class BlogPost {
    // ...
    record TitlesBoundedSumOfLikes(String titles, int boundedSumOfLikes) {};
    // ...
}
```

그런 다음 다음과 같은 방식으로 스트림을 그룹화하고 누적한다.

```java
int maxValLikes = 17;
Map<String, BlogPost.TitlesBoundedSumOfLikes> postsPerAuthor = posts.stream()
  .collect(toMap(BlogPost::getAuthor, post -> {
    int likes = (post.getLikes() > maxValLikes) ? maxValLikes : post.getLikes();
    return new BlogPost.TitlesBoundedSumOfLikes(post.getTitle(), likes);
  }, (u1, u2) -> {
    int likes = (u2.boundedSumOfLikes() > maxValLikes) ? maxValLikes : u2.boundedSumOfLikes();
    return new BlogPost.TitlesBoundedSumOfLikes(u1.titles().toUpperCase() + " : " + u2.titles().toUpperCase(), u1.boundedSumOfLikes() + likes);
  }));
```

toMap의 첫 번째 매개변수는 `BlogPost::getAuthor`를 적용하는 키를 그룹화한다.

두 번째 매개변수는 람다 함수를 사용하여 지도 값을 변환하여 각 BlogPost를 TitlesBoundedSumOfLikes 레코드로 변환한다.

toMap의 세 번째 매개변수는 지정된 키에 대한 중복 요소를 처리하며 여기서는 또 다른 람다 함수를 사용하여 제목을 연결 하고 maxValLikes에 지정된 최대 허용 값으로 좋아요 수를 합산한다.

### 11) 그룹화된 결과를 다른 유형으로 매핑
분류 함수의 결과에 매핑 다운스트림 수집기를 적용하면 더욱 복잡한 집계를 달성할 수 있다.

각 블로그 게시물 유형에 대한 게시물 제목을 연결해 본다.

```java
Map<BlogPostType, String> postsPerType = posts.stream().collect(groupingBy(BlogPost::getType, mapping(BlogPost::getTitle, joining(", ", "Post titles: [", "]"))));
```

여기서 수행한 작업은 각 BlogPost 인스턴스를 제목에 매핑한 다음 게시물 제목 스트림을 연결된 String으로 줄이는 것이다. 이 예에서는 Map 값의 유형도 기본 List 유형과 다르다.

### 12) 반환 맵 유형 수정
groupingBy 컬렉터를 사용할 때 반환된 Map 유형에 대해 가정할 수 없다. 그룹에서 가져오려는 지도 유형을 구체적으로 지정하려면 지도 공급자 함수를 전달하여 지도 유형을 변경할 수 있는 groupingBy 메서드의 세 번째 변형을 사용할 수 있다.

EnumMap 공급자 함수를 groupingBy 메서드에 전달하여 EnumMap을 검색해 본다.

```java
EnumMap<BlogPostType, List<BlogPost>> postsPerType = posts.stream().collect(groupingBy(BlogPost::getType, () -> new EnumMap<>(BlogPostType.class), toList()));
```

## 2. Concurrent groupingBy Collector
groupingBy와 유사하게 멀티 코어 아키텍처를 활용하는 groupingByConcurrent 수집기가 있다. 이 수집기에는 groupingBy 수집기의 해당 오버로드된 메서드와 정확히 동일한 인수를 사용하는 세 가지 오버로드된 메서드가 있다. 그러나 groupingByConcurrent 수집기의 반환 유형은 ConcurrentHashMap 클래스의 인스턴스이거나 해당 클래스의 하위 클래스여야 한다.

그룹화 작업을 동시에 수행하려면 스트림이 병렬이어야 한다.

```java
ConcurrentMap<BlogPostType, List<BlogPost>> postsPerType = posts.parallelStream().collect(groupingByConcurrent(BlogPost::getType));
```

groupingByConcurrent 컬렉터에 Map 공급자 함수를 전달하기로 선택한 경우 함수가 ConcurrentHashMap 또는 그 하위 클래스를 반환하는지 확인해야 한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-groupingby-collector](https://www.baeldung.com/java-groupingby-collector)
