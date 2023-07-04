---
title: Apache Commons Collections MapUtils
author: dejavuhyo
date: 2023-07-05 08:15:00 +0900
categories: [Language, Java]
tags: [apache-commons, apache-commons-maputils, maputils, 아파치-커먼즈]
---

## 1. 메이븐 종속성
종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.1</version>
</dependency>
```

## 2. 활용 방법

### 1) 배열에서 맵 만들기
맵을 만드는데 사용할 배열을 설정한다.

```java
public class MapUtilsTest {
    private String[][] color2DArray = new String[][] {
        {"RED", "#FF0000"},
        {"GREEN", "#00FF00"},
        {"BLUE", "#0000FF"}
    };
    private String[] color1DArray = new String[] {
        "RED", "#FF0000",
        "GREEN", "#00FF00",
        "BLUE", "#0000FF"
    };
    private Map<String, String> colorMap;

    //...
}
```

2차원 배열에서 지도를 만드는 방법이다.

```java
@Test
public void whenCreateMapFrom2DArray_theMapIsCreated() {
    this.colorMap = MapUtils.putAll(
      new HashMap<>(), this.color2DArray);

    assertThat(this.colorMap, is(aMapWithSize(this.color2DArray.length)));
    assertThat(this.colorMap, hasEntry("RED", "#FF0000"));
    assertThat(this.colorMap, hasEntry("GREEN", "#00FF00"));
    assertThat(this.colorMap, hasEntry("BLUE", "#0000FF"));
}
```

1차원 배열을 사용할 수도 있다. 이 경우 배열은 대체 인덱스의 키와 값으로 처리된다.

```java
@Test
public void whenCreateMapFrom1DArray_theMapIsCreated() {
    this.colorMap = MapUtils.putAll(
      new HashMap<>(), this.color1DArray);
    
    assertThat(this.colorMap, is(aMapWithSize(this.color1DArray.length / 2)));
    assertThat(this.colorMap, hasEntry("RED", "#FF0000"));
    assertThat(this.colorMap, hasEntry("GREEN", "#00FF00"));
    assertThat(this.colorMap, hasEntry("BLUE", "#0000FF"));
}
```

### 2) 맵 콘텐츠 인쇄
디버깅하는 동안 또는 디버그 로그에서 여러번 전체 맵을 인쇄한다.

```java
@Test
public void whenVerbosePrintMap_thenMustPrintFormattedMap() {
    MapUtils.verbosePrint(System.out, "Optional Label", this.colorMap);
}
```

결과

```text
Optional Label = 
{
    RED = #FF0000
    BLUE = #0000FF
    GREEN = #00FF00
}
```

값의 데이터 유형을 추가로 인쇄하는 `debugPrint()`를 사용할 수도 있다.

### 3) Values 얻기
MapUtils는 null 안전 방식으로 지정된 키에 대한 맵에서 값을 추출하기 위한 몇 가지 메서드를 제공한다.

예를 들어 `getString()`은 Map에서 문자열을 가져온다. String 값은 `toString()`을 통해 얻는다. 값이 null 이거나 변환에 실패한 경우 반환할 기본값을 선택적으로 지정할 수 있다.

```java
@Test
public void whenGetKeyNotPresent_thenMustReturnDefaultValue() {
    String defaultColorStr = "COLOR_NOT_FOUND";
    String color = MapUtils
      .getString(this.colorMap, "BLACK", defaultColorStr);

    assertEquals(color, defaultColorStr);
}
```

이러한 메서드는 null에 안전하다. 즉, null 맵 매개변수를 안전하게 처리할 수 있다.

```java
@Test
public void whenGetOnNullMap_thenMustReturnDefaultValue() {
    String defaultColorStr = "COLOR_NOT_FOUND";
    String color = MapUtils.getString(null, "RED", defaultColorStr);

    assertEquals(color, defaultColorStr);
}
```

여기서 색상은 맵이 null 임에도 불구하고 COLOR_NOT_FOUND 값을 얻는다.

### 4) 맵 반전
맵을 쉽게 뒤집을 수도 있다.

```java
@Test
public void whenInvertMap_thenMustReturnInvertedMap() {
    Map<String, String> invColorMap = MapUtils.invertMap(this.colorMap);

    int size = invColorMap.size();
    Assertions.assertThat(invColorMap)
      .hasSameSizeAs(colorMap)
      .containsKeys(this.colorMap.values().toArray(new String[] {}))
      .containsValues(this.colorMap.keySet().toArray(new String[] {}));
}
```

이렇게 하면 colorMap이 다음과 같이 반전된다.

```text
{
    #00FF00 = GREEN
    #FF0000 = RED
    #0000FF = BLUE
}
```

소스 맵이 여러 키에 대해 동일한 값을 연결하는 경우 반전 후 값 중 하나가 무작위로 키가 된다.

### 5) Null 및 Empty 검사
`isEmpty()` 메서드는 Map이 null 이거나 비어 있으면 true를 반환한다.

`safeAddToMap()` 메서드는 Map에 null 요소를 추가하는 것을 방지한다.

## 3. 데코레이터
이러한 메서드는 지도에 추가 기능을 추가한다.

대부분의 경우 장식된 Map에 대한 참조를 저장하지 않는 것이 좋다.

### 1) 고정 크기 맵
`fixedSizeMap()`은 주어진 맵이 지원하는 고정 크기 맵을 반환한다. 요소는 변경할 수 있지만 추가하거나 제거할 수는 없다.

```java
@Test(expected = IllegalArgumentException.class)
public void whenCreateFixedSizedMapAndAdd_thenMustThrowException() {
    Map<String, String> rgbMap = MapUtils.fixedSizeMap(MapUtils.putAll(new HashMap<>(), this.color1DArray));

    rgbMap.put("ORANGE", "#FFA500");
}
```

### 2) Predicated 맵
`predicatedMap()` 메서드는 모든 보유 요소가 제공된 술어와 일치하는지 확인하는 Map을 반환한다.

```java
@Test(expected = IllegalArgumentException.class)
public void whenAddDuplicate_thenThrowException() {
    Map<String, String> uniqValuesMap 
      = MapUtils.predicatedMap(this.colorMap, null, 
        PredicateUtils.uniquePredicate());
    
    uniqValuesMap.put("NEW_RED", "#FF0000");
}
```

여기에서 `PredicateUtils.uniquePredicate()`를 사용하여 값에 대한 술어를 지정했다. 이 맵에 중복 값을 삽입하려고 하면 `java.lang.IllegalArgumentException`이 된다.

Predicate 인터페이스를 구현하여 사용자 정의 술어를 구현할 수 있다.

### 3) Lazy 맵
`lazyMap()`은 요청시 값이 초기화되는 맵을 반환한다.

이 맵의 `Map.get(Object)` 메서드에 전달된 키가 지도에 없으면 Transformer 인스턴스를 사용하여 요청된 키와 연결될 새 개체를 만든다.

```java
@Test
public void whenCreateLazyMap_theMapIsCreated() {
    Map<Integer, String> intStrMap = MapUtils.lazyMap(new HashMap<>(), TransformerUtils.stringValueTransformer());

    assertThat(intStrMap, is(anEmptyMap()));

    intStrMap.get(1);
    intStrMap.get(2);
    intStrMap.get(3);
    
    assertThat(intStrMap, is(aMapWithSize(3)));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/apache-commons-map-utils](https://www.baeldung.com/apache-commons-map-utils)
