---
title: Oracle PIVOT
author: dejavuhyo
date: 2021-05-12 10:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-pivot, pivot, 오라클-pivot, 오라클-피벗]
---

## 1. Oracle PIVOT
Oracle 11g는 행을 열로 변환하는 테이블 쿼리를 작성할 수 있는 새로운 PIVOT 절을 도입했다. 따라서 피벗 작업의 출력은 시작 데이터 세트보다 더 많은 열과 더 적은 행을 반환한다.

![oracle-pivot](/assets/img/2021-05-12-oracle-pivot/oracle-pivot.png)

다음은 Oracle PIVOT 절의 기본 구문을 보여 준다.

```sql
SELECT
    select_list
FROM 
    table_name
PIVOT [XML] ( 
    pivot_clause
    pivot_for_clause
    pivot_in_clause
);
```

이 구문에서는 PIVOT 키워드 세 가지가 있다.

* pivot_clause는 집계할 열을 지정한다. pivot_clause는 pivot_in_clause에서 제공하는 값과 함께 절에 지정되지 않은 모든 열을 기준으로 암시적으로 GROUP BY를 수행한다.

* pivot_for_clause는 그룹 또는 피벗할 열을 지정한다.

* pivot_in_clause는 pivot_for_clause의 열에 대한 필터를 정의한다. pivot_in_clause의 각 값에 대한 집계가 별도의 열로 회전된다.

## 2. PIVOT 예제
제품 범주, 주문 상태 및 데모용 주문 ID가 포함된 order_stats라는 새 view를 생성한다.

```sql
CREATE VIEW order_stats AS
SELECT
    category_name,
    status, 
    order_id
FROM
    order_items
INNER JOIN orders USING (order_id)
INNER JOIN products USING (product_id)
INNER JOIN product_categories USING (category_id);
```

다음은 order_stats view의 부분 데이터이다.

```sql
SELECT * FROM order_stats;
```

![sample-view-partial-data](/assets/img/2021-05-12-oracle-pivot/sample-view-partial-data.png)

이 예제에서는 PIVOT 절을 사용하여 주문 상태별로 각 제품 범주의 주문 수를 반환한다.

```sql
SELECT * FROM order_stats
PIVOT(
    COUNT(order_id)
    FOR category_name
    IN (
        'CPU',
        'Video Card',
        'Mother Board',
        'Storage'
    )
)
ORDER BY status;
```

이 예에서는 다음을 수행합니다.

* COUNT(order_id)는 pivot_clause이다.

* FOR category_name는 pivot_for_clause이다.

다음은 pivot_in_clause이다.

```sql
IN (
    'CPU',
    'Video Card',
    'Mother Board',
    'Storage'
)
```

COUNT() 기능은 범주별 주문 수와 주문 상태를 반환한다. 쿼리는 결과 집합의 열 머리글에 대해 pivot_in_clause에 지정된 값을 사용한다.

다음은 결과이다.

![pivot-example](/assets/img/2021-05-12-oracle-pivot/pivot-example.png)

## 3. PIVOT 열 별칭 지정
이전 예에서 오라클은 제품 범주를 사용하여 피벗 열 이름을 생성했다. 반면 pivot_clause에서는 하나 이상의 열에 별칭을 붙이고 pivot_in_clause에서는 하나 이상의 값에 별칭을 붙일 수 있다.

일반적으로 Oracle은 다음과 같은 규칙을 사용하여 별칭을 기반으로 피벗 열의 이름을 지정한다.

| Pivot Column Aliased? | Pivot In-Value Aliased? | Pivot Column Name |
|:-----:|:-----:|:-----:|
| No | No | pivot_in_clause value |
| Yes | Yes | pivot_in_clause alias `|| '_' ||` pivot_clause alias |
| No | Yes | pivot_in_clause alias |
| Yes | No | pivot_in_clause value `|| '_' ||` pivot_clause alias |

다음 문에서는 위의 쿼리 예를 별칭과 함께 사용합니다.

```sql
SELECT * FROM order_stats
PIVOT(
    COUNT(order_id) order_count
    FOR category_name
    IN (
        'CPU' CPU,
        'Video Card' VideoCard, 
        'Mother Board' MotherBoard,
        'Storage' Storage
    )
)
ORDER BY status;
```

다음은 결과이다.

![aliasing-example](/assets/img/2021-05-12-oracle-pivot/aliasing-example.png)

보시다시피 피벗 열 이름은 아래 명명 규칙을 따른다.

```text
pivot_in_clause alias || '_' || pivot_clause alias
```

pivot_clause에서 둘 이상의 집계 함수를 사용하는 경우 하나 이상의 집계 함수에 대한 별칭을 제공해야 한다.

## 4. 여러 열 PIVOT
이전 예에서는 pivot_clause에서 aggregate 함수 하나를 사용했다. 다음 예에서는 두 개의 aggregate 함수를 사용한다.

* **첫째:** order_stats 보기를 변경하여 order 값 열을 포함한다.

```sql
CREATE OR REPLACE VIEW order_stats AS
SELECT 
    category_name,
    status,
    order_id,
    SUM(quantity * list_price) AS order_value
FROM
    order_items
INNER JOIN orders USING (order_id)
INNER JOIN products USING (product_id)
INNER JOIN product_categories USING (category_id)
GROUP BY
    order_id,
    status,
    category_name;
```

* **둘째:** 새 order_stats view에서 데이터를 조회한다.

```sql
SELECT * FROM order_stats;
```

![sample-view](/assets/img/2021-05-12-oracle-pivot/sample-view.png)

* **셋째:** PIVOT 절을 사용하여 제품 범주 및 주문 상태별 주문 수와 주문 값을 반환한다.

```sql
SELECT * FROM order_stats
PIVOT(
    COUNT(order_id) orders,
    SUM(order_value) sales
    FOR category_name
    IN (
        'CPU' CPU,
        'Video Card' VideoCard,
        'Mother Board' MotherBoard,
        'Storage' Storage
    )
)
ORDER BY status;
```

다음은 결과이다.

![pivoting-multiple-columns](/assets/img/2021-05-12-oracle-pivot/pivoting-multiple-columns.png)

## 5. PIVOT with 하위 쿼리
pivot_in_clause에서는 하위 쿼리를 사용할 수 없다. 다음 문이 잘못되어 오류가 발생한다.

```sql
SELECT * FROM order_stats
PIVOT(
    COUNT(order_id) orders,
    SUM(order_value) sales
    FOR category_name
    IN
        SELECT category_name
        FROM product_categories
    )
)
ORDER BY status;
```

다음은 오류 메시지이다.

```text
ORA-00936: missing expression
```

이 제한은 XML 옵션에서 완화된다.

```sql
SELECT * FROM order_stats
PIVOT XML (
    COUNT(order_id) orders,
    SUM(order_value) sales
    FOR category_name
    IN (
        SELECT category_name
        FROM product_categories
    )
)
ORDER BY status;
```

다음은 결과이다.

![pivot-xml](/assets/img/2021-05-12-oracle-pivot/pivot-xml.png)

다음은 하나의 PivotSet 샘플이다.

```xml
<PivotSet>
    <item>
        <column name="CATEGORY_NAME">CPU</column>
        <column name="ORDERS">13</column>
        <column name="SALES">4122040.7</column>
    </item>
    <item>
        <column name="CATEGORY_NAME">Mother Board</column>
        <column name="ORDERS">12</column>
        <column name="SALES">679121.39</column>
    </item>
    <item>
        <column name="CATEGORY_NAME">RAM</column>
        <column name="ORDERS">0</column>
        <column name="SALES" />
    </item>
    <item>
        <column name="CATEGORY_NAME">Storage</column>
        <column name="ORDERS">14</column>
        <column name="SALES">3023747.6</column>
    </item>
    <item>
        <column name="CATEGORY_NAME">Video Card</column>
        <column name="ORDERS">9</column>
        <column name="SALES">1677597.4</column>
    </item>
</PivotSet>
```

SQL Developer의 출력 그리드에서 XML을 보려면 다음 단계를 수행하여 XML을 설정한다.

① Tool 메뉴에서 Preferences을 선택한다.

![sql-developer-preferences](/assets/img/2021-05-12-oracle-pivot/sql-developer-preferences.png)

② Database → Advanced에서 Display XML Value in Grid 옵션을 선택한다.

![sql-developer-display-xml-in-grid](/assets/img/2021-05-12-oracle-pivot/sql-developer-display-xml-in-grid.png)

XML 출력 형식이 non-XML 피벗 형식과 다르다. pivot_in_clause에 지정된 각 값에 대해 하위 쿼리는 단일 XML 문자열 열을 반환한다.

각 행의 XML 문자열에는 해당 행의 암시적 GROUP BY 값에 해당하는 집계된 데이터(예: 주문 수(ORDERS) 및 총 판매(SALES)가 포함된다.

pivot_in_clause에서 하위 쿼리를 사용하면 Oracle은 하위 쿼리에서 반환된 모든 값을 피벗에 사용한다.

하위 쿼리는 고유한 값 목록을 반환해야 한다. 그렇지 않으면 Oracle에서 런타임 오류가 발생한다. 하위 쿼리가 고유 값 목록을 반환하는지 여부를 잘 모를 경우 하위 쿼리에서 DISTINCT 키워드를 사용할 수 있다.

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-basics/oracle-pivot/>
