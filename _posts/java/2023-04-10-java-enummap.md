---
title: Java EnumMap
author: dejavuhyo
date: 2023-04-10 08:20:00 +0900
categories: [Language, Java]
tags: [java-enummap, enummap, 자바-이넘맵, 이넘맵]
---

## 1. 설정
요일에 플레이하는 스포츠와 요일을 매핑해야 하는 간단한 요구 사항이 있다.

```java
Monday     Soccer
Tuesday    Basketball
Wednesday  Hiking
Thursday   Karate
```

이를 위해 enum을 사용할 수 있다.

```java
public enum DayOfWeek {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
```

## 2. Creation
EnumMap 탐색을 시작하려면 먼저 인스턴스화해야 한다.

```
EnumMap<DayOfWeek, String> activityMap = new EnumMap<>(DayOfWeek.class);
activityMap.put(DayOfWeek.MONDAY, "Soccer");
```

그리고 여기에 HashMap과 같은 더 일반적인 것과의 첫 번째 차이점이 있다. HashMap을 사용하면 유형 매개변수화가 충분하므로 `new HashMap<>()`을 사용하지 않아도 된다. **그러나 EnumMap에는 생성자에 키 유형이 필요하다.**

### 1) EnumMap 복사 생성자
EnumMap은 또한 두 개의 복사 생성자와 함께 제공된다. 첫 번째는 다른 EnumMap을 사용한다.

```java
EnumMap<DayOfWeek, String> activityMap = new EnumMap<>(DayOfWeek.class);
activityMap.put(DayOfWeek.MONDAY, "Soccer");
activityMap.put(DayOfWeek.TUESDAY, "Basketball");

EnumMap<DayOfWeek, String> activityMapCopy = new EnumMap<>(dayMap);
assertThat(activityMapCopy.size()).isEqualTo(2);
assertThat(activityMapCopy.get(DayOfWeek.MONDAY)).isEqualTo("Soccer");
assertThat(activityMapCopy.get(DayOfWeek.TUESDAY)).isEqualTo("Basketball");
```

### 2) Map 복사 생성자
또는 키가 enum인 비어 있지 않은 Map이 있는 경우에도 그렇게 할 수 있다.

```java
Map<DayOfWeek, String> ordinaryMap = new HashMap();
ordinaryMap.put(DayOfWeek.MONDAY, "Soccer");

EnumMap enumMap = new EnumMap(ordinaryMap);
assertThat(enumMap.size()).isEqualTo(1);
assertThat(enumMap.get(DayOfWeek.MONDAY)).isEqualTo("Soccer");
```
**EnumMap이 기존 항목에서 키 유형을 결정할 수 있도록 Map이 비어 있지 않아야 한다.**

지정된 Map에 둘 이상의 enum 유형이 포함된 경우 생성자는 ClassCastException을 발생시킨다.

## 3. 요소 추가 및 검색
EnumMap을 인스턴스화한 후 `put()` 메서드를 사용하여 스포츠를 추가할 수 있다.

```java
activityMap.put(DayOfWeek.MONDAY, "Soccer");
```

그리고 이를 검색하기 위해 `get()`을 사용할 수 있다.

```java
assertThat(activityMap.get(DayOfWeek.MONDAY)).isEqualTo("Soccer");
```

## 4. 요소 확인
특정 날짜에 매핑이 정의되어 있는지 확인하려면 `containsKey()`를 사용한다.

```java
activityMap.put(DayOfWeek.WEDNESDAY, "Hiking");
assertThat(activityMap.containsKey(DayOfWeek.WEDNESDAY)).isTrue();
```

그리고 특정 스포츠가 키에 매핑되어 있는지 확인하려면 `containsValue()`를 사용한다.

```java
assertThat(activityMap.containsValue("Hiking")).isTrue();
```

### 1) null as Value
이제 null은 EnumMap에 대해 의미상 유효한 값이다.

null을 "doing nothing"과 연결하고 토요일에 매핑한다.

```java
assertThat(activityMap.containsKey(DayOfWeek.SATURDAY)).isFalse();
assertThat(activityMap.containsValue(null)).isFalse();
activityMap.put(DayOfWeek.SATURDAY, null);
assertThat(activityMap.containsKey(DayOfWeek.SATURDAY)).isTrue();
assertThat(activityMap.containsValue(null)).isTrue();
```

## 5. 요소 제거
특정 날짜의 매핑을 해제하려면 간단히 `remove()`하면 된다.

```java
activityMap.put(DayOfWeek.MONDAY, "Soccer");
assertThat(activityMap.remove(DayOfWeek.MONDAY)).isEqualTo("Soccer");
assertThat(activityMap.containsKey(DayOfWeek.MONDAY)).isFalse();
```

알 수 있듯이 `remove(key)`는 키와 관련된 이전 값을 반환하거나 키에 대한 매핑이 없는 경우 null을 반환한다.

특정 날짜가 특정 활동에 매핑된 경우에만 특정 날짜의 매핑을 해제하도록 선택할 수도 있다.

```java
activityMap.put(DayOfWeek.Monday, "Soccer");
assertThat(activityMap.remove(DayOfWeek.Monday, "Hiking")).isEqualTo(false);
assertThat(activityMap.remove(DayOfWeek.Monday, "Soccer")).isEqualTo(true);
```

`remove(key, value)` 키가 현재 지정된 값에 매핑된 경우에만 지정된 키에 대한 항목을 제거한다.

## 6. Collection Views
일반 Map과 마찬가지로 모든 EnumMap을 사용하여 3개의 다른 보기 또는 하위 컬렉션을 가질 수 있다.

먼저 activityMap을 생성한다.

```java
EnumMap<DayOfWeek, String> activityMap = new EnumMap(DayOfWeek.class);
activityMap.put(DayOfWeek.THURSDAY, "Karate");
activityMap.put(DayOfWeek.WEDNESDAY, "Hiking");
activityMap.put(DayOfWeek.MONDAY, "Soccer");
```

### 1) values
액티비티 Map의 첫 번째 뷰는 `values()`이며 이름에서 알 수 있듯이 Map의 모든 값을 반환한다.

```java
Collection values = dayMap.values();
assertThat(values)
  .containsExactly("Soccer", "Hiking", "Karate");
```

**여기서 EnumMap은 순서가 지정된 Map이다. DayOfWeek 열거형의 순서를 사용하여 항목의 순서를 결정한다.**

### 2) keySet
`keySet()`은 다시 열거형 순서로 키 모음을 반환한다.

```java
Set keys = dayMap.keySet();
assertThat(keys)
        .containsExactly(DayOfWeek.MONDAY, DayOfWeek.WEDNESDAY, DayOfWeek.SATURDAY);
```

### 3) entrySet
`entrySet()`은 키와 값 쌍으로 매핑을 반환한다.

```java
assertThat(dayMap.entrySet())
    .containsExactly(
        new SimpleEntry(DayOfWeek.MONDAY, "Soccer"),
        new SimpleEntry(DayOfWeek.WEDNESDAY, "Hiking"),
        new SimpleEntry(DayOfWeek.THURSDAY, "Karate")
    );
```

### 4) Mutability
**원본 액티비티 Map에서 변경한 사항은 모든 뷰에 반영된다.**

```java
activityMap.put(DayOfWeek.TUESDAY, "Basketball");
assertThat(values)
    .containsExactly("Soccer", "Basketball", "Hiking", "Karate");
```

반대로 하위 보기에 대한 모든 변경 사항은 원래 활동 Map에 반영된다.

```java
values.remove("Hiking");
assertThat(activityMap.containsKey(DayOfWeek.WEDNESDAY)).isFalse();
assertThat(activityMap.size()).isEqualTo(3);
```

Map 인터페이스를 사용한 EnumMap의 계약에 따라 하위 보기는 원본 Map에 의해 지원된다.

## 7. EnumMap을 사용하는 경우

### 1) 성능
Enum을 키로 사용하면 **가능한 모든 키를 미리 알고 있기 때문에 더 빠른 해시 계산과 같은 추가 성능 최적화를 수행할 수 있다.**

enum을 키로 사용하는 단순성은 EnumMap이 저장 및 검색을 위한 매우 간단한 논리를 사용하여 일반 Java 배열로만 백업하면 됨을 의미한다. 반면에 일반 Map 구현은 일반 개체를 키로 사용하는 것과 관련된 문제를 처리해야 한다. 예를 들어, HashMap은 해시 충돌의 가능성을 수용하기 위해 복잡한 데이터 구조와 훨씬 더 복잡한 저장 및 검색 논리가 필요하다.

### 2) 기능
또한 EnumMap은 보기가 열거형 순서로 반복된다는 점에서 정렬된 Map이다. 보다 복잡한 시나리오에서 유사한 동작을 얻으려면 TreeMap 또는 LinkedHashMap을 볼 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-enum-map](https://www.baeldung.com/java-enum-map)
