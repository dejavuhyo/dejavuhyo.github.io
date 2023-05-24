---
title: Java List를 Map으로 변환
author: dejavuhyo
date: 2023-05-24 09:50:00 +0900
categories: [Language, Java]
tags: [java-convert, list-map, 리스트-맵, 자바-변환]
---

## 1. 샘플 데이터 구조
요소를 모델링한다.

```java
public class Animal {
    private int id;
    private String name;

    //  constructor/getters/setters
}
```

id 필드는 고유하므로 키로 만들 수 있다.

전통적인 방법으로 변환을 시작한다.

## 2. 자바 8 이전
핵심 Java 메서드를 사용하여 List를 Map으로 변환할 수 있다.

```java
public Map<Integer, Animal> convertListBeforeJava8(List<Animal> list) {
    Map<Integer, Animal> map = new HashMap<>();
    for (Animal animal : list) {
        map.put(animal.getId(), animal);
    }
    return map;
}
```

변환을 테스트한다.

```java
@Test
public void givenAList_whenConvertBeforeJava8_thenReturnMapWithTheSameElements() {
    Map<Integer, Animal> map = convertListService
      .convertListBeforeJava8(list);
    
    assertThat(
      map.values(), 
      containsInAnyOrder(list.toArray()));
}
```

## 3. 자바 8 사용
Java 8부터 Streams와 Collectors를 사용하여 List를 Map으로 변환할 수 있다.

```java
public Map<Integer, Animal> convertListAfterJava8(List<Animal> list) {
    Map<Integer, Animal> map = list.stream()
      .collect(Collectors.toMap(Animal::getId, Function.identity()));
    return map;
}
```

변환이 올바르게 수행되었는지 확인한다.

```java
@Test
public void givenAList_whenConvertAfterJava8_thenReturnMapWithTheSameElements() {
    Map<Integer, Animal> map = convertListService.convertListAfterJava8(list);
    
    assertThat(
      map.values(), 
      containsInAnyOrder(list.toArray()));
}
```

## 4. 구아바 라이브러리 사용
핵심 Java 외에도 변환을 위해 타사 라이브러리를 사용할 수 있다.

### 1) 메이븐 구성
`pom.xml`에 다음 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.1-jre</version>
</dependency>
```

### 2) Maps.uniqueIndex()를 사용하여 변환
`Maps.uniqueIndex()` 메서드를 사용하여 List를 Map으로 변환한다.

```java
public Map<Integer, Animal> convertListWithGuava(List<Animal> list) {
    Map<Integer, Animal> map = Maps
      .uniqueIndex(list, Animal::getId);
    return map;
}
```

변환을 테스트한다.

```java
@Test
public void givenAList_whenConvertWithGuava_thenReturnMapWithTheSameElements() {
    Map<Integer, Animal> map = convertListService
      .convertListWithGuava(list);
    
    assertThat(
      map.values(), 
      containsInAnyOrder(list.toArray()));
}
```

## 5. Apache Commons Library 사용
Apache Commons Library 방법으로 변환할 수도 있다.

### 1) 메이븐 구성
Maven 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.4</version>
</dependency>
```

### 2) MapUtils
`MapUtils.populateMap()`을 사용하여 변환을 수행한다.

```java
public Map<Integer, Animal> convertListWithApacheCommons(List<Animal> list) {
    Map<Integer, Animal> map = new HashMap<>();
    MapUtils.populateMap(map, list, Animal::getId);
    return map;
}
```

변환을 테스트한다.

```java
@Test
public void givenAList_whenConvertWithApacheCommons_thenReturnMapWithTheSameElements() {
    Map<Integer, Animal> map = convertListService
      .convertListWithApacheCommons(list);
    
    assertThat(
      map.values(), 
      containsInAnyOrder(list.toArray()));
}
```

## 7. Values 충돌
id 필드가 고유하지 않은 경우 어떻게 되는지 확인한다.

### 1) ID가 중복 된 동물 목록
고유하지 않은 ID를 가진 Animal 목록을 만든다.

```java
@Before
public void init() {

    this.duplicatedIdList = new ArrayList<>();

    Animal cat = new Animal(1, "Cat");
    duplicatedIdList.add(cat);
    Animal dog = new Animal(2, "Dog");
    duplicatedIdList.add(dog);
    Animal pig = new Animal(3, "Pig");
    duplicatedIdList.add(pig);
    Animal cow = new Animal(4, "Cow");
    duplicatedIdList.add(cow);
    Animal goat= new Animal(4, "Goat");
    duplicatedIdList.add(goat);
}
```

`Cow`와 `Goat`는 같은 id를 가진다.

### 2) 확인
Java Map의 `put()` 메서드는 가장 최근에 추가된 값이 이전 값을 동일한 키로 덮어쓰도록 구현된다.

이러한 이유로 기존 변환과 Apache Commons `MapUtils.populateMap()`은 동일한 방식으로 작동한다.

```java
@Test
public void givenADupIdList_whenConvertBeforeJava8_thenReturnMapWithRewrittenElement() {

    Map<Integer, Animal> map = convertListService
      .convertListBeforeJava8(duplicatedIdList);

    assertThat(map.values(), hasSize(4));
    assertThat(map.values(), hasItem(duplicatedIdList.get(4)));
}

@Test
public void givenADupIdList_whenConvertWithApacheCommons_thenReturnMapWithRewrittenElement() {

    Map<Integer, Animal> map = convertListService
      .convertListWithApacheCommons(duplicatedIdList);

    assertThat(map.values(), hasSize(4));
    assertThat(map.values(), hasItem(duplicatedIdList.get(4)));
}
```

`Goat`가 같은 id를 가진 `Cow`를 덮어쓰는 것을 볼 수 있다.

그러나 `Collectors.toMap()` 및 `MapUtils.populateMap()`은 각각 `IllegalStateException` 및 `IllegalArgumentException`을 발생시킨다.

```java
@Test(expected = IllegalStateException.class)
public void givenADupIdList_whenConvertAfterJava8_thenException() {

    convertListService.convertListAfterJava8(duplicatedIdList);
}

@Test(expected = IllegalArgumentException.class)
public void givenADupIdList_whenConvertWithGuava_thenException() {

    convertListService.convertListWithGuava(duplicatedIdList);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-list-to-map](https://www.baeldung.com/java-list-to-map)
