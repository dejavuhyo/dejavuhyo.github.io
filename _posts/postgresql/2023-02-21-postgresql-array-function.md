---
title: PostgreSQL 배열 함수
author: dejavuhyo
date: 2023-02-21 21:50:00 +0900
categories: [Database, PostgreSQL]
tags: [postgresql-array, array-function, postgresql, unnest, array-agg, array-to-string, postgresql-unnest, postgresql-array-agg, postgresql-array-to-string, postgresql-배열-함수, postgresql-배열, postgresql-함수]
---

## 1. UNNEST
`UNNEST` 함수는 Array의 개수만큼 row로 변환한다. Array 데이터 사용 시 많이 사용하는 함수로 `sum()` 통계함수와 같이 사용하면 Array의 개수만큼 값이 중복된다.

```sql
SELECT col1, UNNEST(col2) AS col2 FROM table;
```

```text
|col1|unnest|
|----|------|
|채소 |배추   |
|채소 |상추   |
|채소 |양파   |
|과일 |배     |
|과일 |귤     |
|과일 |수박   |
|과일 |참외   |
|과일 |복숭아 |
```

## 2. ARRAY_AGG, ARRAY_TO_STRING
row를 Array 또는 String으로 변환한다.

`ARRAY_AGG`는 `group by`와 함께 사용하는 함수로 `group by` 된 값들을 Array로 반환한다.

`distinct`와 `order by`를 함께 이용하면 중복으로 제거된 값을 구하거나, 원하는 순서로 정렬하여 Array를 만들 수 있다. (`ARRAY_AGG(distinct col2 order by col2)`)

```sql
SELECT col1, ARRAY_AGG(col2), ARRAY_TO_STRING(ARRAY_AGG(col2), ',');
FROM table
GROUP BY col1;
```

```text
|col1|array_agg      |array_to_string|
|----|---------------|---------------|
|과일 |{배,귤,수박,참외,복숭아}|배,귤,수박,참외,복숭아 |
|채소 |{배추,상추,양파}       |배추,상추,양파         |
```

## 3. STRING_TO_ARRAY
String을 특정 문자로 분리하여 Array로 변환한다.

```sql
SELECT col1, STRING_TO_ARRAY(col1, ',')
FROM table;
```

```text
|col1         |string_to_array|
|-------------|---------------|
|귤,배,복숭아,수박,참외|{귤,배,복숭아,수박,참외}|
```

## [출처 및 참고]
* [https://codecamp.tistory.com/9](https://codecamp.tistory.com/9)
