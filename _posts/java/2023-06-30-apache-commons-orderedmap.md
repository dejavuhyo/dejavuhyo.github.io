---
title: Apache Commons Collections OrderedMap
author: dejavuhyo
date: 2023-06-30 08:30:00 +0900
categories: [Language, Java]
tags: [apache-commons, apache-commons-orderedmap, orderedmap, 아파치-커먼즈]
---

## 1. 메이븐 종속성
`pom.xml`에 Maven 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.1</version>
</dependency>
```

[Maven Central 저장소](https://central.sonatype.com/artifact/org.apache.commons/commons-collections4)에서 최신 버전의 라이브러리를 찾을 수 있다.

## 2. OrderedMap 속성
OrderedMap 인터페이스를 구현하는 맵은 다음과 같다.

* 세트가 정렬되지 않더라도 키 세트에서 순서를 유지한다.

* 메서드를 사용하여 양방향으로 반복할 수 있다. `firstKey()` 및 `nextKey()` 또는 `lastKey()` 및 `previousKey()`

* MapIterator로 순회 가능하다. (또한 라이브러리에서 제공)

* 요소를 찾고, 변경하고, 제거하고, 교체하는 방법을 제공한다.

## 3. OrderedMap 사용
테스트 클래스에서 러너와 연령의 OrderedMap을 설정한다. 라이브러리에서 제공되는 OrderedMap 구현 중 하나인 LinkedMap을 사용한다.

먼저 Map을 로드하고 값의 순서를 확인하는데 사용할 주자와 연령의 배열을 설정한다.

```java
public class OrderMapUnitTest {
    private String[] names = {"Emily", "Mathew", "Rose", "John", "Anna"};
    private Integer[] ages = {37, 28, 40, 36, 21};
    private LinkedMap<String, Integer> runnersLinkedMap;

    //...
}
```

맵을 초기화한다.

```java
@Before
public void createRunners() {
    this.runnersLinkedMap = new LinkedMap<>();
    
    for (int i = 0; i < RUNNERS_COUNT; i++) {
        runners.put(this.names[i], this.ages[i]);
    }
}
```

### 1) 순방향 반복
순방향 반복자가 어떻게 사용되는지 확인한다.

```java
@Test
public void givenALinkedMap_whenIteratedForwards_thenPreservesOrder() {
    String name = this.runnersLinkedMap.firstKey();
    int i = 0;
    while (name != null) {
        assertEquals(name, names[i]);
        name = this.runnersLinkedMap.nextKey(name);
        i++;
    }
}
```

마지막 키에 도달하면 `nextKey()` 메서드가 null 값을 반환한다.

### 2) 역방향 반복
마지막 키부터 시작하여 다시 반복한다.

```java
@Test
public void givenALinkedMap_whenIteratedBackwards_thenPreservesOrder() {
    String name = this.runnersLinkedMap.lastKey();
    int i = RUNNERS_COUNT - 1;
    while (name != null) {
        assertEquals(name, this.names[i]);
        name = this.runnersLinkedMap.previousKey(name);
        i--;
    }
}
```

첫 번째 키에 도달하면 `previousKey()` 메서드는 null을 반환한다.

### 3) MapIterator 예제
`mapIterator()` 메서드를 사용하여 배열 이름 및 연령에 정의된 대로 러너의 순서를 유지하는 방법을 보여주면서 MapIterator를 가져온다.

```java
@Test
public void givenALinkedMap_whenIteratedWithMapIterator_thenPreservesOrder() {
    OrderedMapIterator<String, Integer> runnersIterator = this.runnersLinkedMap.mapIterator();

    int i = 0;
    while (runnersIterator.hasNext()) {
        runnersIterator.next();

        assertEquals(runnersIterator.getKey(), this.names[i]);
        assertEquals(runnersIterator.getValue(), this.ages[i]);
        i++;
    }
}
```

### 4) 요소 제거
인덱스나 객체로 요소를 제거하는 방법이다.

```java
@Test
public void givenALinkedMap_whenElementRemoved_thenSizeDecrease() {
    LinkedMap<String, Integer> lmap 
      = (LinkedMap<String, Integer>) this.runnersLinkedMap;

    Integer johnAge = lmap.remove("John");

    assertEquals(johnAge, new Integer(36));
    assertEquals(lmap.size(), RUNNERS_COUNT - 1);

    Integer emilyAge = lmap.remove(0);

    assertEquals(emilyAge, new Integer(37));
    assertEquals(lmap.size(), RUNNERS_COUNT - 2);
}
```

## 4. 제공 구현
라이브러리 버전 4.1에는 OrderedMap 인터페이스의 두 가지 구현인 ListOrderedMap 및 LinkedMap이 있다.

ListOrderedMap은 `java.util.List`를 사용하여 키 세트의 순서를 추적한다. 이것은 OrderedMap의 데코레이터 정적 메서드 `ListOrderedMap.decorate(Map map)`를 사용하여 모든 Map에서 만들 수 있다.

LinkedMap은 HashMap을 기반으로 하며 양방향 반복 및 OrderedMap 인터페이스의 다른 메서드를 허용하여 HashMap을 개선한다.

두 구현 모두 OrderedMap 인터페이스 외부에 있는 세 가지 메서드도 제공한다.

* `asList()`: 맵의 순서를 유지하는 `List<K>` 유형의 목록을 얻는다(여기서 K 는 키 유형임).

* `get(int index)`: 인터페이스에서 제공되는 `get(Object o)` 메서드와 달리 index 위치에 있는 요소를 가져온다.

* `indexOf(Object o)`: 정렬된 맵에서 객체 o의 인덱스를 가져온다.

OrderedMap을 LinkedMap으로 캐스팅하여 `asList()` 메서드를 사용할 수 있다.

```java
@Test
public void givenALinkedMap_whenConvertedToList_thenMatchesKeySet() {
    LinkedMap<String, Integer> lmap 
      = (LinkedMap<String, Integer>) this.runnersLinkedMap;

    List<String> listKeys = new ArrayList<>();
    listKeys.addAll(this.runnersLinkedMap.keySet());
    List<String> linkedMap = lmap.asList();

    assertEquals(listKeys, linkedMap);
}
```

LinkedMap 구현에서 `indexOf(Object o)` 및 `get(int index)` 메서드의 기능을 확인할 수 있다.

```java
@Test
public void givenALinkedMap_whenSearchByIndexIsUsed_thenMatchesConstantArray() {
    LinkedMap<String, Integer> lmap = (LinkedMap<String, Integer>) this.runnersLinkedMap;

    for (int i = 0; i < RUNNERS_COUNT; i++) {
        String name = lmap.get(i);

        assertEquals(name, this.names[i]);
        assertEquals(lmap.indexOf(this.names[i]), i);
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/apache-commons-ordered-map](https://www.baeldung.com/apache-commons-ordered-map)
