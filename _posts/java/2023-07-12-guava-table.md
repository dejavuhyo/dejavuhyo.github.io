---
title: Guava 테이블
author: dejavuhyo
date: 2023-07-12 08:30:00 +0900
categories: [Language, Java]
tags: [guava-table, guava, 구아바, 구아바-테이블]
---

## 1. 구글 구아바 테이블
Table 클래스를 사용하는 방법이다.

### 1) 메이븐 종속성
`pom.xml`에 Google의 Guava 라이브러리 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.0.1-jre</version>
</dependency>
```

최신 버전은 [여기](https://central.sonatype.com/artifact/org.netbeans.external/com-google-guava)에서 찾을 수 있다.

### 2) 개요
핵심 Java에 있는 컬렉션을 사용하여 Guava의 테이블을 나타내려면 구조는 각 row에 연결된 셀 값이 있는 columns 맵을 포함하는 rows 맵이 된다.

테이블은 단일 값을 참조하기 위해 결합된 방식으로 두 개의 키를 지정할 수 있는 특수 맵을 나타낸다.

예를 들어 `Map<UniversityName, Map<CoursesOffered, SeatAvailable>>` 과 같은 지도의 지도를 만드는 것과 비슷하다. 테이블은 Battleships 게임 보드를 나타내는 완벽한 방법이기도 하다.

## 2. 생성
여러 가지 방법으로 Table의 인스턴스를 만들 수 있다.

내부적으로 LinkedHashMap을 사용하는 HashBasedTable 클래스의 create 메소드 사용한다.

```java
Table<String, String, Integer> universityCourseSeatTable = HashBasedTable.create();
```

row 키와 column 키를 자연 순서로 정렬하거나 비교기를 제공하여 정렬해야 하는 테이블이 필요한 경우 내부적으로 TreeMap을 사용하는 TreeBasedTable 클래스의 create 메서드를 사용하여 테이블 인스턴스를 만들 수 있다.

```java
Table<String, String, Integer> universityCourseSeatTable = TreeBasedTable.create();
```

row 키와 column 키를 미리 알고 테이블 크기가 고정된 경우 ArrayTable 클래스의 create 메서드를 사용한다.

```java
List<String> universityRowTable = Lists.newArrayList("Mumbai", "Harvard");
List<String> courseColumnTables = Lists.newArrayList("Chemical", "IT", "Electrical");
Table<String, String, Integer> universityCourseSeatTable = ArrayTable.create(universityRowTable, courseColumnTables);
```

내부 데이터가 절대 변경되지 않는 불변 테이블 인스턴스를 만들려면 ImmutableTable 클래스를 사용한다(빌더 패턴을 따라 생성).

```java
Table<String, String, Integer> universityCourseSeatTable = ImmutableTable.<String, String, Integer> builder().put("Mumbai", "Chemical", 120).build();
```

## 3. 사용
Table의 사용법을 보여주는 간단한 예제이다.

### 1) 검색
row 키와 column 키를 알고 있으면 row 및 column 키와 관련된 값을 얻을 수 있다.

```java
@Test
public void givenTable_whenGet_returnsSuccessfully() {
    Table<String, String, Integer> universityCourseSeatTable = HashBasedTable.create();
    universityCourseSeatTable.put("Mumbai", "Chemical", 120);
    universityCourseSeatTable.put("Mumbai", "IT", 60);
    universityCourseSeatTable.put("Harvard", "Electrical", 60);
    universityCourseSeatTable.put("Harvard", "IT", 120);

    int seatCount = universityCourseSeatTable.get("Mumbai", "IT");
    Integer seatCountForNoEntry = universityCourseSeatTable.get("Oxford", "IT");

    assertThat(seatCount).isEqualTo(60);
    assertThat(seatCountForNoEntry).isEqualTo(null);
}
```

### 2) 항목 확인
다음을 기반으로 테이블에 항목이 있는지 확인할 수 있다.

* Row 키

* Column 키

* row 키와 column 키 모두

* 값

항목이 있는지 확인하는 방법이다.

```java
@Test
public void givenTable_whenContains_returnsSuccessfully() {
    Table<String, String, Integer> universityCourseSeatTable = HashBasedTable.create();
    universityCourseSeatTable.put("Mumbai", "Chemical", 120);
    universityCourseSeatTable.put("Mumbai", "IT", 60);
    universityCourseSeatTable.put("Harvard", "Electrical", 60);
    universityCourseSeatTable.put("Harvard", "IT", 120);

    boolean entryIsPresent = universityCourseSeatTable.contains("Mumbai", "IT");
    boolean courseIsPresent = universityCourseSeatTable.containsColumn("IT");
    boolean universityIsPresent = universityCourseSeatTable.containsRow("Mumbai");
    boolean seatCountIsPresent = universityCourseSeatTable.containsValue(60);

    assertThat(entryIsPresent).isEqualTo(true);
    assertThat(courseIsPresent).isEqualTo(true);
    assertThat(universityIsPresent).isEqualTo(true);
    assertThat(seatCountIsPresent).isEqualTo(true);
}

```

### 3) 제거
row 키와 column 키를 모두 제공하여 테이블에서 항목을 제거할 수 있다.

```java
@Test
public void givenTable_whenRemove_returnsSuccessfully() {
    Table<String, String, Integer> universityCourseSeatTable
      = HashBasedTable.create();
    universityCourseSeatTable.put("Mumbai", "Chemical", 120);
    universityCourseSeatTable.put("Mumbai", "IT", 60);

    int seatCount = universityCourseSeatTable.remove("Mumbai", "IT");

    assertThat(seatCount).isEqualTo(60);
    assertThat(universityCourseSeatTable.remove("Mumbai", "IT")).isEqualTo(null);
}
```

### 4) 셀 값 맵에 대한 Row 키
column 키를 제공하여 키를 row로 하고 값을 CellValue로 하는 맵 표현을 얻을 수 있다.

```java
@Test
public void givenTable_whenColumn_returnsSuccessfully() {
    Table<String, String, Integer> universityCourseSeatTable = HashBasedTable.create();
    universityCourseSeatTable.put("Mumbai", "Chemical", 120);
    universityCourseSeatTable.put("Mumbai", "IT", 60);
    universityCourseSeatTable.put("Harvard", "Electrical", 60);
    universityCourseSeatTable.put("Harvard", "IT", 120);

    Map<String, Integer> universitySeatMap = universityCourseSeatTable.column("IT");

    assertThat(universitySeatMap).hasSize(2);
    assertThat(universitySeatMap.get("Mumbai")).isEqualTo(60);
    assertThat(universitySeatMap.get("Harvard")).isEqualTo(120);
}
```

### 5) 테이블의 맵 표현
columnMap 메서드를 사용하여 `Map<UniversityName, Map<CoursesOffered, SeatAvailable>>` 표현을 얻을 수 있다.

```java
@Test
public void givenTable_whenColumnMap_returnsSuccessfully() {
    Table<String, String, Integer> universityCourseSeatTable = HashBasedTable.create();
    universityCourseSeatTable.put("Mumbai", "Chemical", 120);
    universityCourseSeatTable.put("Mumbai", "IT", 60);
    universityCourseSeatTable.put("Harvard", "Electrical", 60);
    universityCourseSeatTable.put("Harvard", "IT", 120);

    Map<String, Map<String, Integer>> courseKeyUniversitySeatMap = universityCourseSeatTable.columnMap();

    assertThat(courseKeyUniversitySeatMap).hasSize(3);
    assertThat(courseKeyUniversitySeatMap.get("IT")).hasSize(2);
    assertThat(courseKeyUniversitySeatMap.get("Electrical")).hasSize(1);
    assertThat(courseKeyUniversitySeatMap.get("Chemical")).hasSize(1);
}
```

### 6) 셀 값 맵에 대한 Column 키
row 키를 제공하여 키를 column로, 값을 CellValue로 사용하여 맵 표현을 얻을 수 있다.

```java
@Test
public void givenTable_whenRow_returnsSuccessfully() {
    Table<String, String, Integer> universityCourseSeatTable = HashBasedTable.create();
    universityCourseSeatTable.put("Mumbai", "Chemical", 120);
    universityCourseSeatTable.put("Mumbai", "IT", 60);
    universityCourseSeatTable.put("Harvard", "Electrical", 60);
    universityCourseSeatTable.put("Harvard", "IT", 120);

    Map<String, Integer> courseSeatMap = universityCourseSeatTable.row("Mumbai");

    assertThat(courseSeatMap).hasSize(2);
    assertThat(courseSeatMap.get("IT")).isEqualTo(60);
    assertThat(courseSeatMap.get("Chemical")).isEqualTo(120);
}
```

### 7) 고유 Row 키 가져오기
rowKeySet 메서드를 사용하여 테이블에서 모든 row 키를 가져올 수 있다.

```java
@Test
public void givenTable_whenRowKeySet_returnsSuccessfully() {
    Table<String, String, Integer> universityCourseSeatTable = HashBasedTable.create();
    universityCourseSeatTable.put("Mumbai", "Chemical", 120);
    universityCourseSeatTable.put("Mumbai", "IT", 60);
    universityCourseSeatTable.put("Harvard", "Electrical", 60);
    universityCourseSeatTable.put("Harvard", "IT", 120);

    Set<String> universitySet = universityCourseSeatTable.rowKeySet();

    assertThat(universitySet).hasSize(2);
}
```

### 8) 고유 Column 키 가져오기
columnKeySet 메서드를 사용하여 테이블에서 모든 column 키를 가져올 수 있다.

```java
@Test
public void givenTable_whenColKeySet_returnsSuccessfully() {
    Table<String, String, Integer> universityCourseSeatTable = HashBasedTable.create();
    universityCourseSeatTable.put("Mumbai", "Chemical", 120);
    universityCourseSeatTable.put("Mumbai", "IT", 60);
    universityCourseSeatTable.put("Harvard", "Electrical", 60);
    universityCourseSeatTable.put("Harvard", "IT", 120);

    Set<String> courseSet = universityCourseSeatTable.columnKeySet();

    assertThat(courseSet).hasSize(3);
}
```

## [출처 및 참고]
* [https://www.baeldung.com/guava-table](https://www.baeldung.com/guava-table)
