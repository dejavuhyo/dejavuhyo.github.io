---
title: Oracle ROW_NUMBER
author: dejavuhyo
date: 2021-05-07 06:30:00 +0900
categories: [Database, Oracle]
tags: [oracle-row-number, row-number, 오라클-row-number, 오라클-조회-순번]
---

## 1. ROW_NUMBER() 기능
ROW_NUMBER()는 파티션의 각 행 또는 결과 집합의 각 행에 순차 고유 정수를 할당하는 분석 함수이다.

다음은 ROW_NUMBER() 함수의 구문을 보여준다.

```sql
ROW_NUMBER() OVER (
    [query_partition_clause]
    order_by_clause
)
```

이 구문에서는 다음을 수행한다.

order_by_clause가 필요하다. 각 파티션 또는 전체 결과 집합의 행 순서를 지정한다. order_by_clause의 형식은 다음과 같다.

```sql
ORDER BY expression1 [,expression2,...] [ASC | DESC ] [NULLS FIRST | LAST]
```

query_partition_clause는 선택 사항이다. 함수가 적용되는 파티션으로 행을 분배한다. query_partition_clause를 생략하면 함수는 전체 결과 집합을 단일 파티션으로 처리한다. query_partition_clause의 형식은 다음과 같다.

```sql
PARTITION BY expression1 [,expression2, ...]
```

## 2. ROW_NUMBER() 예제
샘플 데이터베이스의 products 테이블을 사용하여 ROW_NUMBER() 기능을 보여준다.

![products-table](/assets/img/2021-05-07-oracle-row-number/products-table.png)

### 1) ROW_NUMBER() 간단한 예제
다음 명령문은 제품 테이블에서 행 번호, 제품 이름 및 정가를 반환한다. 행 번호 값은 리스트 가격 순서에 따라 할당된다.

```sql
SELECT
    ROW_NUMBER() OVER(
        ORDER BY list_price DESC
    ) row_num,
    product_name,
    list_price
FROM
    products;
```

다음 그림은 결과를 보여준다.

![row-number-example](/assets/img/2021-05-07-oracle-row-number/row-number-example.png)

ROW_NUMBER() 함수의 작동 방식을 보여주는 간단한 예제이다.

ROW_NUMBER() 함수를 효과적으로 사용하려면 하위 쿼리 또는 공통 테이블 식을 사용하여 지정된 범위의 행 번호를 검색하여 top-N, bottom-N 그리고 inner-N 결과를 가져와야 한다.

### 2) 페이지 매김에 ROW_NUMBER() 기능 사용
ROW_NUMBER() 기능은 응용 프로그램의 페이지 매김에 유용하다.

높은 가격에서 낮은 가격까지 페이지별로 제품을 표시하려고 하면 각 페이지에는 10개의 제품이 있다. 세 번째 페이지를 표시하려면 다음과 같이 ROW_NUMBER() 기능을 사용한다.

```sql
WITH cte_products AS (
    SELECT
        row_number() OVER(
            ORDER BY list_price DESC
        ) row_num,
        product_name,
        list_price
    FROM
        products
)
SELECT * FROM cte_products
WHERE row_num > 30 and row_num <= 40;
```

결과는 다음과 같다.

![row-number-for-pagination](/assets/img/2021-05-07-oracle-row-number/row-number-for-pagination.png)

이 예제에서 CTE는 ROW_NUMBER() 함수를 사용하여 각 행에 내림차순으로 순차 정수를 할당했다. 외부 쿼리가 행 번호가 31에서 40 사이의 행을 검색했다.

### 3) top-N 쿼리 예제에 ROW_NUMBER() 함수 사용
범주별로 가장 비싼 제품 하나를 가져오려면, 다음 쿼리에 표시된 ROW_NUMBER() 함수를 사용할 수 있다.

```sql
WITH cte_products AS (
SELECT 
    row_number() OVER(
        PARTITION BY category_id
        ORDER BY list_price DESC
    ) row_num,
    category_id,
    product_name,
    list_price
FROM
    products
)
SELECT * FROM cte_products
WHERE row_num = 1;
```

결과는 다음과 같다.

![row-number-for-top-n-query](/assets/img/2021-05-07-oracle-row-number/row-number-for-top-n-query.png)

이 예에서 다음을 수행한다.

* __첫째:__ PARTITION BY 절은 행을 범주 ID별로 파티션으로 나눈다.

* __둘째:__ ORDER BY 절에서는 정가별로 각 범주의 제품을 내림차순으로 정렬했다.

* __셋째:__ ROW_NUMBER() 함수는 특정 카테고리 ID의 각 행에 적용된다. 각 범주의 행 번호를 다시 초기화했다.

* __넷째:__ 외부 쿼리는 각 범주에서 가장 비싼 제품인 행 번호 1을 가진 행을 선택했다.


일관된 결과를 얻으려면 쿼리가 결정적 순서로 결과 집합을 반환해야 한다. 예를 들어, 두 제품의 가격이 동일하다면, 결과는 일관되지 않을 것이다. 첫 번째 또는 두 번째 제품을 반환할 수 있다.

동일한 N-highest 가격의 제품을 두 개 이상 얻으려면 RANK() 또는 DENSE_RANK() 함수를 사용할 수 있다.

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-analytic-functions/oracle-row_number/>
