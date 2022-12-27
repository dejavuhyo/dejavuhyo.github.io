---
title: 쿼리 실행 순서
author: dejavuhyo
date: 2022-08-25 10:20:00 +0900
categories: [Database, SQL]
tags: [queries-order-execution, select-queries, queries-order-execution, queries-order, 쿼리-실행-순서, 쿼리-순서, 실행-순서]
---

## 1. 쿼리 실행 순서
각 쿼리는 데이터베이스에서 필요한 데이터를 찾은 다음 해당 데이터를 최대한 빨리 처리하고 이해할 수 있는 것으로 필터링하는 것으로 시작한다. 쿼리의 각 부분이 순차적으로 실행되기 때문에 실행 순서를 파악하여 어디에서 어떤 결과를 액세스할 수 있는지 파악하는 것이 중요하다.

> `FROM and JOIN` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT / OFFSET`

```sql
SELECT DISTINCT column, AGG_FUNC(column_or_expression), ...
FROM mytable
    JOIN another_table
      ON mytable.column = another_table.column
    WHERE constraint_expression
    GROUP BY column
    HAVING constraint_expression
    ORDER BY column ASC/DESC
    LIMIT count OFFSET COUNT;
```

### 1) FROM and JOIN
`FROM` 절과 후속 `JOIN`이 먼저 실행되어 쿼리되는 데이터의 총 작업 세트를 결정한다. 여기에는 이 절의 하위 쿼리가 포함되며, 결합되는 테이블의 모든 열과 행을 포함하는 후드 아래에 임시 테이블이 작성될 수 있다.

### 2) WHERE
전체 데이터 작업 세트를 확보하면 제약 조건이 개별 행에 적용되고 제약 조건을 충족하지 않는 행은 폐기된다.

각 제약 조건은 `FROM` 절에서 요청한 테이블에서 직접 열에만 액세스할 수 있다.

쿼리의 `SELECT` 부분에 있는 별칭은 아직 실행되지 않은 쿼리의 부분에 종속된 식을 포함할 수 있으므로 대부분의 데이터베이스에서 액세스할 수 없다.

### 3) GROUP BY
`WHERE` 제약 조건이 적용된 후 나머지 행은 `GROUP BY` 절에 지정된 열의 공통 값을 기준으로 그룹화된다.

그룹화하면 해당 열에 고유한 값이 있는 행 개수만 표시된다. 즉, 쿼리에 집계 함수가 있는 경우에만 이 기능을 사용해야 한다.

### 4) HAVING
쿼리에 `GROUP BY` 절이 있으면 `HAVING` 절의 제약 조건이 그룹화된 행에 적용된다. 제약 조건을 충족하지 않는 그룹화된 행을 삭제한다.

`WHERE` 절과 마찬가지로 대부분의 데이터베이스에서 이 단계에서 별칭에 액세스할 수 없습니다.

### 5) SELECT
쿼리의 `SELECT` 부분에 있는 식이 최종적으로 계산된다.

### 6) DISTINCT
나머지 행 중 중복된 값이 `DISTINCT`로 표시된 열에 있는 행은 삭제된다.

### 7) ORDER BY
`ORDER BY` 절에서 순서를 지정한 경우 행은 지정된 데이터를 기준으로 오름차순 또는 내림차순으로 정렬된다.

쿼리의 `SELECT` 부분에 있는 모든 식이 계산되었으므로 이 절에서 별칭을 참조할 수 있다.

### 8) LIMIT / OFFSET
마지막으로 `LIMIT` 및 `OFFSET`에서 지정한 범위를 벗어나는 행은 삭제되고 마지막 행 집합은 쿼리에서 반환된다.

## [출처 및 참고]
* <https://sqlbolt.com/lesson/select_queries_order_of_execution>
