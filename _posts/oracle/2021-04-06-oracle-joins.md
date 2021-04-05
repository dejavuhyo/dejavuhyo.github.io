---
title: Oracle Joins
author: dejavuhyo
date: 2021-04-06 06:00:00 +0900
categories: [Database, Oracle]
tags: [oracle-joins, inner-join, left-join, left-outer-join, right-join, right-outer-join, full-outer-join, 오라클-joins]
---

## 1. Summary
Oracle join은 관련 열의 값을 기준으로 둘 이상의 테이블의 열을 결합하는 데 사용된다. 관련 열은 일반적으로 첫 번째 테이블의 기본 키 열과 두 번째 테이블의 외부 키 열이다.

Oracle은 inner join, left join, right join, full outer join 그리고 cross join을 지원한다.

inner join, left join, 또는 right join을 사용하여 계층 데이터를 쿼리하기 위해 테이블을 자체 결합할 수 있다. 이런 종류의 조인은 self-join 이라고 한다.

## 2. Sample Tables

* Create SQL

```sql
CREATE TABLE palette_a (
    id INT PRIMARY KEY,
    color VARCHAR2 (100) NOT NULL
);

CREATE TABLE palette_b (
    id INT PRIMARY KEY,
    color VARCHAR2 (100) NOT NULL
);

-- insert data for the palette_a
INSERT INTO palette_a (id, color)
VALUES (1, 'Red');

INSERT INTO palette_a (id, color)
VALUES (2, 'Green');

INSERT INTO palette_a (id, color)
VALUES (3, 'Blue');

INSERT INTO palette_a (id, color)
VALUES (4, 'Purple');

-- insert data for the palette_b
INSERT INTO palette_b (id, color)
VALUES (1, 'Green');

INSERT INTO palette_b (id, color)
VALUES (2, 'Red');

INSERT INTO palette_b (id, color)
VALUES (3, 'Cyan');

INSERT INTO palette_b (id, color)
VALUES (4, 'Brown');
```

* palett_a Table

![palette-a-table](/assets/img/2021-04-06-oracle-joins/palette-a-table.png)

* palett_b Table

![palette-b-table](/assets/img/2021-04-06-oracle-joins/palette-b-table.png)

## 3. Oracle inner join
색상 열의 값을 사용하여 왼쪽 테이블을 오른쪽 테이블로 결합한다.

* Query

```sql
SELECT
    a.id id_a,
    a.color color_a,
    b.id id_b,
    b.color color_b
FROM
    palette_a a
INNER JOIN palette_b b
ON a.color = b.color
```

* 결과

![inner-join-example](/assets/img/2021-04-06-oracle-joins/inner-join-example.png)

inner join은 오른쪽 테이블의 행과 일치하는 왼쪽 테이블의 행을 반환한다.

* Venn diagram

![inner-join-venn-diagram](/assets/img/2021-04-06-oracle-joins/inner-join-venn-diagram.png)

## 4. Oracle left join
left join(또는 left outer join)을 사용하여 왼쪽 테이블을 오른쪽 테이블과 결합한다.

* Query

```sql
SELECT
    a.id id_a,
    a.color color_a,
    b.id id_b,
    b.color color_b
FROM
    palette_a a
LEFT JOIN palette_b b
ON a.color = b.color
```

* 결과

![left-join-example](/assets/img/2021-04-06-oracle-joins/left-join-example.png)

left join에서는 오른쪽 테이블에서 사용할 수 있는 경우 일치하는 행이 있는 왼쪽 테이블의 모든 행을 반환한다. 오른쪽 테이블에서 일치하는 행을 찾을 수 없는 경우 left join에는 오른쪽 테이블의 열에 대한 null 값이 포함된다.

* Venn diagram

![left-join-venn-diagram](/assets/img/2021-04-06-oracle-joins/left-join-venn-diagram.png)

왼쪽 테이블에 없는 행만 가져오려는 경우도 있다. 이를 위해 left join 및 WHERE 절을 사용하여 오른쪽 테이블에서 행을 제외한다.

다음 SQL은 palette_a에서만 사용할 수 있고 palette_b에서는 사용할 수 없는 색상이 표시된다.

* Query

```sql
SELECT
    a.id id_a,
    a.color color_a,
    b.id id_b,
    b.color color_b
FROM
    palette_a a
LEFT JOIN palette_b b
ON a.color = b.color
WHERE
    b.id IS NULL
```

* 결과

![left-join-example-with-where](/assets/img/2021-04-06-oracle-joins/left-join-example-with-where.png)

* Venn diagram

![left-join-with-where-venn-diagram](/assets/img/2021-04-06-oracle-joins/left-join-with-where-venn-diagram.png)

## 5. Oracle right join
right join 또는 right outer join은 left join의 역 버전이다. right join에서는 오른쪽 테이블의 모든 행과 왼쪽 테이블의 일치하는 행을 포함하는 결과 집합을 만든다. 일치하는 항목이 없으면 왼쪽이 null 이다.

right join을 사용하여 왼쪽 테이블을 오른쪽 테이블로 조인한다.

* Query

```sql
SELECT
    a.id id_a,
    a.color color_a,
    b.id id_b,
    b.color color_b
FROM
    palette_a a
RIGHT JOIN palette_b b
ON a.color = b.color
```

* 결과

![right-join-example](/assets/img/2021-04-06-oracle-joins/right-join-example.png)

* Venn diagram

![right-join-venn-diagram](/assets/img/2021-04-06-oracle-joins/right-join-venn-diagram.png)

WHERE 절을 추가하여 오른쪽 테이블에서만 행을 가져올 수 있지만 왼쪽 테이블에서는 가져올 수 없다.

* Query

```sql
SELECT
    a.id id_a,
    a.color color_a,
    b.id id_b,
    b.color color_b
FROM
    palette_a a
RIGHT JOIN palette_b b
ON a.color = b.color
WHERE
    a.id IS NULL
```

* 결과

![right-join-example-with-where](/assets/img/2021-04-06-oracle-joins/right-join-example-with-where.png)

* Venn diagram

![right-join-with-where-venn-diagram](/assets/img/2021-04-06-oracle-joins/right-join-with-where-venn-diagram.png)

## 6. Oracle full outer join
full outer join 또는 full join은 왼쪽 및 오른쪽 테이블의 모든 행을 포함하는 결과 집합을 반환하며 사용 가능한 경우 양쪽에서 일치하는 행을 포함한다. 일치하는 항목이 없으면 누락 된쪽에 null이 된다.

왼쪽 및 오른쪽 테이블의 전체 외부 조인을 보여준다.

* Query

```sql
SELECT
    a.id id_a,
    a.color color_a,
    b.id id_b,
    b.color color_b
FROM
    palette_a a
FULL OUTER JOIN palette_b b
ON a.color = b.color
```

* 결과

![full-outer-join-example](/assets/img/2021-04-06-oracle-joins/full-outer-join-example.png)

OUTER 키워드는 선택 사항이다.

* Venn diagram

![full-outer-join-venn-diagram](/assets/img/2021-04-06-oracle-joins/full-outer-join-venn-diagram.png)

왼쪽 및 오른쪽 테이블로부터 고유한 행 집합을 가져오려면 동일한 full join을 수행 후 WHERE 절을 사용하여 양쪽에서 원하지 않는 행을 제외한다.

* Query

```sql
SELECT
    a.id id_a,
    a.color color_a,
    b.id id_b,
    b.color color_b
FROM
    palette_a a
FULL JOIN palette_b b
ON a.color = b.color
WHERE
    a.id IS NULL OR b.id IS NULL
```

* 결과

![full-outer-join-example-with-where](/assets/img/2021-04-06-oracle-joins/full-outer-join-example-with-where.png)

* Venn diagram

![full-outer-join-with-where-venn-diagram](/assets/img/2021-04-06-oracle-joins/full-outer-join-with-where-venn-diagram.png)

## [출처 및 참고]
* <https://www.oracletutorial.com/oracle-basics/oracle-joins/>
