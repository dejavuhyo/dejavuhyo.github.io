---
title: Java HashMap 초기화
author: dejavuhyo
date: 2023-04-18 10:10:00 +0900
categories: [Language, Java]
tags: [java-initialize-hashmap, initialize-hashmap, java-hashmap, 자바-맵-초기화, 맵-초기화, 자바-해시맵]
---

## 1.Static HashMap의 정적 초기화
정적 코드 블록을 사용하여 HashMap을 초기화할 수 있다.

```java
public static Map<String, String> articleMapOne;
static {
    articleMapOne = new HashMap<>();
    articleMapOne.put("ar01", "Intro to Map");
    articleMapOne.put("ar02", "Some article");
}
```

이러한 종류의 초기화의 장점은 맵이 변경 가능하지만 정적인 경우에만 작동한다는 것이다. 따라서 필요에 따라 항목을 추가하고 제거할 수 있다.

```java
@Test
public void givenStaticMap_whenUpdated_thenCorrect() {
    
    MapInitializer.articleMapOne.put(
      "NewArticle1", "Convert array to List");
    
    assertEquals(
      MapInitializer.articleMapOne.get("NewArticle1"), 
      "Convert array to List");  
}
```

이중 중괄호 구문을 사용하여 맵을 초기화할 수도 있다.

```java
Map<String, String> doubleBraceMap  = new HashMap<String, String>() {{
    put("key1", "value1");
    put("key2", "value2");
}};
```

**이 초기화 기술은 사용할 때마다 익명의 추가 클래스를 생성하고, 둘러싸는 개체에 대한 숨겨진 참조를 보유하며, 메모리 누수 문제를 일으킬 수 있기 때문에 이 초기화 기술을 피해야 한다.**

## 2. 자바 컬렉션 사용
단일 항목이 있는 변경 불가능한 싱글톤 맵을 만들어야 하는 경우 `Collections.singletonMap()`이 매우 유용하다.

```java
public static Map<String, String> createSingletonMap() {
    return Collections.singletonMap("username1", "password1");
}
```

여기서 맵은 변경할 수 없으며 더 많은 항목을 추가하려고 하면 `java.lang.UnsupportedOperationException`이 발생한다.

`Collections.emptyMap()`을 사용하여 변경할 수 없는 빈 맵을 만들 수도 있다.

```java
Map<String, String> emptyMap = Collections.emptyMap();
```

## 3. Java 8 방식
Java 8 Stream API를 사용하여 맵을 초기화하는 방법이다.

### 1) Collectors.toMap() 사용
2차원 문자열 배열의 Stream을 사용하고 맵으로 수집한다.

```java
Map<String, String> map = Stream.of(new String[][] {
  { "Hello", "World" }, 
  { "John", "Doe" }, 
}).collect(Collectors.toMap(data -> data[0], data -> data[1]));
```

여기서 맵의 키와 값의 데이터 유형이 동일하다는 점에 유의한다.

좀 더 일반적으로 만들기 위해 개체 배열을 가져와 동일한 작업을 수행한다.

```java
Map<String, Integer> map = Stream.of(new Object[][] { 
     { "data1", 1 }, 
     { "data2", 2 }, 
}).collect(Collectors.toMap(data -> (String) data[0], data -> (Integer) data[1]));
```

결과적으로 키는 String으로, 값은 Integer로 매핑하는 맵을 생성한다.

### 2) Map.Entry 스트림 사용
`Map.Entry`의 인스턴스를 사용한다. 이것은 키와 값 유형이 다른 또 다른 접근 방식이다.

먼저 Entry 인터페이스의 SimpleEntry 구현을 사용한다.

```java
Map<String, Integer> map = Stream.of(
  new AbstractMap.SimpleEntry<>("idea", 1), 
  new AbstractMap.SimpleEntry<>("mobile", 2))
  .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
```

SimpleImmutableEntry 구현을 사용하여 맵을 만든다.

```java
Map<String, Integer> map = Stream.of(
  new AbstractMap.SimpleImmutableEntry<>("idea", 1),    
  new AbstractMap.SimpleImmutableEntry<>("mobile", 2))
  .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
```

### 3) 변경할 수 없는 맵 초기화
특정 사용 사례에서는 변경할 수 없는 맵을 초기화해야 한다. 이는 `Collectors.toMap()` 내부에 `Collectors.collectingAndThen()`을 래핑하여 수행할 수 있다.

```java
Map<String, String> map = Stream.of(new String[][] { 
    { "Hello", "World" }, 
    { "John", "Doe" },
}).collect(Collectors.collectingAndThen(
    Collectors.toMap(data -> data[0], data -> data[1]), 
    Collections::<String, String> unmodifiableMap));
```

**스트림을 사용하여 이러한 초기화를 사용하는 것은 피해야 한다. 엄청난 성능 오버헤드가 발생할 수 있고 맵을 초기화하기 위해 많은 가비지 객체가 생성될 수 있기 때문이다.**

## 4. Java 9 방식
Java 9에는 변경할 수 없는 맵의 생성 및 초기화를 단순화하는 Map 인터페이스의 다양한 팩터리 메서드가 제공된다.

### 1) Map.of()
이 팩토리 메서드는 인수 없음, 단일 인수 및 가변 인수를 사용한다.

```java
Map<String, String> emptyMap = Map.of();
Map<String, String> singletonMap = Map.of("key1", "value");
Map<String, String> map = Map.of("key1","value1", "key2", "value2");
```

이 방법은 최대 10개의 key-value 쌍만 지원한다.

### 2) Map.ofEntries()
`Map.of()`와 유사 하지만 key-value 쌍의 수에 제한이 없다.

```java
Map<String, String> map = Map.ofEntries(
  new AbstractMap.SimpleEntry<String, String>("name", "John"),
  new AbstractMap.SimpleEntry<String, String>("city", "budapest"),
  new AbstractMap.SimpleEntry<String, String>("zip", "000000"),
  new AbstractMap.SimpleEntry<String, String>("home", "1231231231")
);
```

팩터리 메서드는 변경할 수 없는 맵을 생성하므로 모든 변경으로 인해 UnsupportedOperationException이 발생한다.

또한 null 키 또는 중복 키를 허용하지 않는다.

이제 초기화 후 변경 가능하거나 성장하는 맵이 필요한 경우 맵 인터페이스의 구현을 생성하고 이러한 변경 불가능한 맵을 생성자에 전달할 수 있다.

```java
Map<String, String> map = new HashMap<String, String> (
  Map.of("key1","value1", "key2", "value2"));
Map<String, String> map2 = new HashMap<String, String> (
  Map.ofEntries(
    new AbstractMap.SimpleEntry<String, String>("name", "John"),
    new AbstractMap.SimpleEntry<String, String>("city", "budapest")));
```

## 5. 구아바 사용
Guava 라이브러리를 사용하여 맵을 초기화한다.

```java
Map<String, String> articles 
  = ImmutableMap.of("Title", "My New Article", "Title2", "Second Article");
```

변경불가능한 맵이 생성되고, 변경가능한 맵도 생성된다.

```java
Map<String, String> articles 
  = Maps.newHashMap(ImmutableMap.of("Title", "My New Article", "Title2", "Second Article"));
```

`ImmutableMap.of()` 메서드에는 최대 5쌍의 key-value 매개변수를 사용할 수 있는 오버로드된 버전도 있다. 2쌍의 매개변수가 있는 예는 다음과 같다.

```java
ImmutableMap.of("key1", "value1", "key2", "value2");
```

## [출처 및 참고]
* [https://www.baeldung.com/java-initialize-hashmap](https://www.baeldung.com/java-initialize-hashmap)
