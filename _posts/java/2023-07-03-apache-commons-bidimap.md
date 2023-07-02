---
title: Apache Commons Collections BidiMap
author: dejavuhyo
date: 2023-07-03 08:20:00 +0900
categories: [Language, Java]
tags: [apache-commons, apache-commons-bidimap, bidimap, 아파치-커먼즈]
---

## 1. 메이븐 종속성
BidiMap과 그 구현을 사용하려면 프로젝트에 다음 종속성을 포함해야 한다. Maven 기반 프로젝트의 경우 `pom.xml`에 다음 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.1</version>
</dependency>
```

Gradle 기반 프로젝트의 경우 `build.gradle` 파일에 동일한 아티팩트를 추가한다.

```text
compile 'org.apache.commons:commons-collections4:4.1'
```

종속성의 최신 버전은 [Maven Central](https://central.sonatype.com/artifact/org.apache.commons/commons-collections4)에서 찾을 수 있다.

## 2. 구현 및 인스턴스화
BidiMap 자체는 양방향 Map에 고유한 동작을 정의하는 인터페이스일 뿐이며 물론 여러 구현이 가능하다.

BidiMap의 구현은 키와 값의 중복을 허용하지 않는다. BidiMap이 반전 되면 중복 값이 ​​중복 키로 변환되어 맵 계약을 위반하게 된다. 맵에는 항상 고유한 키가 있어야 한다.

* DualHashBidiMap: 이 구현은 두 개의 HashMap 인스턴스를 사용하여 BidiMap을 내부적으로 구현한다. 항목의 키 또는 값을 사용하여 항목을 빠르게 조회할 수 있다. 단, HashMap 인스턴스는 2개를 유지해야 한다.

* DualLinkedHashBidiMap: 이 구현은 두 개의 LinkedHashMap 인스턴스를 사용 하고 결과적으로 맵 항목의 삽입 순서를 유지한다. 유지 관리할 맵 항목의 삽입 순서가 필요하지 않은 경우 저렴한 DualHashBidiMap을 사용할 수 있다.

* TreeBidiMap: 이 구현은 효율적이며 Red-Black 트리 구현으로 실현된다. TreeBidiMap의 키와 값은 키와 값의 자연스러운 순서를 사용하여 오름차순으로 정렬되도록 보장된다.

* TreeBidiMap과 동일한 것을 달성하기 위해 TreeMap의 두 인스턴스를 사용하는 DualTreeBidiMap도 있다. DualTreeBidiMap은 분명히 TreeBidiMap 보다 비싸다.

BidiMap 인터페이스는 `java.util.Map` 인터페이스를 확장하므로 이를 대신할 수 있다. 구체적인 구현의 no-arg 생성자를 사용하여 구체적인 개체 인스턴스를 인스턴스화 할 수 있다.

## 3. 독특한 BidiMap 방법
인터페이스에 고유한 메서드이다.

### 1) put()
맵에 새로운 키-값 항목을 삽입한다. 새 항목의 값이 기존 항목의 값과 일치하면 새 항목을 위해 기존 항목이 제거된다.

메서드는 제거된 이전 항목을 반환하거나 없는 경우 null을 반환한다.

```java
BidiMap<String, String> map = new DualHashBidiMap<>();
map.put("key1", "value1");
map.put("key2", "value2");
assertEquals(map.size(), 2);
```

### 2) inverseBidiMap()
BidiMap의 키-값 쌍을 뒤집는다. 이 메서드는 키가 값이 된 새 BidiMap을 반환하고 그 반대도 마찬가지이다. 이 작업은 번역 및 사전 응용 프로그램에서 매우 유용할 수 있다.

```java
BidiMap<String, String> rMap = map.inverseBidiMap();
assertTrue(rMap.containsKey("value1") && rMap.containsKey("value2"));
```

### 3) removeValue()
키 대신 값을 지정하여 맵 항목을 제거하는데 사용된다. 이것은 `java.util` 패키지에 있는 Map 구현에 대한 추가 사항이다.

```java
map.removeValue("value2");
assertFalse(map.containsKey("key2"));
```

### 4) getKey()
BidiMap의 특정 값에 매핑된 키를 가져올 수 있다. 이 메서드는 지정된 값에 매핑된 키가 없는 경우 null을 반환한다.

```java
assertEquals(map.getKey("value1"), "key1");
```

## [출처 및 참고]
* [https://www.baeldung.com/commons-collections-bidi-map](https://www.baeldung.com/commons-collections-bidi-map)
