---
title: Oracle DECODE
author: dejavuhyo
date: 2021-03-18 06:05:00 +0900
categories: [Database, Oracle]
tags: [oracle-decode, decode, 오라클-decode]
---

## 1. DECODE 목적
DECODE는 expr을 각 검색 값과 하나씩 비교한다. expr이 검색과 동일하면 Oracle Database는 해당 결과를 반환한다. 일치하는 항목이 없으면 Oracle은 기본값을 반환한다. 기본값이 생략되면 Oracle은 null을 반환한다.

변수는 숫자 유형(NUMBER, BINIAN_FLOAT 또는 BINIAN_DUBLE) 또는 문자 유형 중 하나일 수 있다.

* 확장 및 검색이 문자 데이터인 경우 Oracle은 패드가 없는 비교 의미론을 사용하여 해당 데이터를 비교한다. expr, search 및 result는 CHAR, VARCAR2, NCHAR 또는 NVARCAR2 데이터 유형 중 하나일 수 있다. 반환된 문자열은 VARCAR2 데이터 형식이며 첫 번째 결과 매개 변수와 동일한 문자 집합에 있다.

* 첫 번째 search-result 쌍이 숫자일 경우 Oracle은 모든 search-result 식과 첫 번째 식을 비교하여 가장 높은 숫자 우선 순위를 가진 인수를 결정하고 나머지 인수를 해당 데이터 형식으로 암시적으로 변환한 다음 해당 데이터 유형을 반환한다.


검색, 결과 및 기본값은 식에서 파생될 수 있다. Oracle Database는 단락 평가를 사용한다. 즉, 데이터베이스는 검색 값을 expr과 비교하기 전에 모든 검색 값을 평가하지 않고 expr과 비교하기 전에만 각 검색 값을 평가한다. 따라서 Oracle은 이전 검색이 expr과 동일한 경우 검색을 평가하지 않는다.

Oracle은 비교하기 전에 expr 및 각 검색 값을 첫 번째 검색 값의 데이터 형식으로 자동 변환한다. Oracle은 반환 값을 첫 번째 결과와 동일한 데이터 형식으로 자동 변환한다. 첫 번째 결과에 데이터 유형 CHAR이 있거나 첫 번째 결과가 null인 경우 Oracle은 반환 값을 VARCAR2 데이터 유형으로 변환한다.

디코드 함수에서 Oracle은 두 개의 null을 동등하다고 간주한다. expr이 null이면 Oracle은 null이기도 한 첫 번째 검색 결과를 반환한다.

A의 DECODE기능, 오라클은 상응하는 두 널 (null)을 고려한다. 경우 exprnull의 경우, 오라클은 반환 result최초의 search도 널 (null)입니다.

expr, 검색, 결과 및 기본값을 포함하여 DECODE 함수의 최대 구성 요소 수는 255개이다.

## 2. 문법

![decode](/assets/img/2021-03-18-oracle-decode/decode.gif)

```sql
DECODE(expr, search, result
        [, search, result ]...
        [, default ]
    )
```

## 3. 활용

### 1) 일반적인 예제
부서번호가 10이면 ACCOUNTING, 20이면 RESEARCH, 30이면 SALES 나머지는 OPERATIONS를 출력

* SQL

```sql
SELECT
    deptno, DECODE(deptno, 10, 'ACCOUNTING'
                        , 20, 'RESEARCH'
                        , 30, 'SALES'
                        , 'OPERATIONS') name
FROM
    dept
```

* 결과

```text
DEPTNO NAME
------ ----------
    10 ACCOUNTING
    20 RESEARCH
    30 SALES
    40 OPERATIONS
```

### 2) DECODE 함수에서 집계 함수를 사용
10부서는 급여합계를, 20부서는 최대값을, 30부서는 최소값을 출력

* SQL

```sql
SELECT
    deptno, DECODE(deptno, 10, SUM(sal)
                        , 20, MAX(sal)
                        , 30, MIN(sal)) sal
FROM
    emp
GROUP BY
    deptno
```

* 결과

```text
DEPTNO    SAL
------ ------
    30    950
    20   3000
    10   8750
```

### 3) 집계함수와 함께 통계 데이터를 추출
부서별로 급여 합계를 출력

* SQL

```sql
SELECT
    deptno, NVL(SUM(DECODE(deptno, 10, sal)),0) deptno10
        , NVL(SUM(DECODE(deptno, 20, sal)),0) deptno20
        , NVL(SUM(DECODE(deptno, 30, sal)),0) deptno30
        , NVL(SUM(DECODE(deptno, 40, sal)),0) deptno40
FROM
    emp
GROUPB Y
    deptno
```

* 결과

```text
DEPTNO  DEPTNO10  DEPTNO20  DEPTNO30  DEPTNO40
------ --------- --------- --------- ---------
    30         0         0      9400         0
    20         0     10875         0         0
    10      8750         0         0         0
```


### 4) 행으로 합계 조회
부서별로 급여 합계를 행으로 출력

* SQL

```sql
SELECT
    d.deptno, NVL(SUM(e.sal),0) sal
FROM
    emp e, dept d
WHERE
    e.deptno(+) = d.deptno
GROUP BY
    d.deptno
```

* 결과

```text
DEPTNO      SAL
------ --------
    10     8750
    20    10875
    30     9400
    40        0
```

### 5) DECODE와 MAX함수를 사용하여 열로 합계 조회
부서별로 급여 합계를 열로 출력

* SQL

```sql
SELECT
    MAX(NVL(SUM(DECODE(deptno, 10, sal)),0)) deptno10
    , MAX(NVL(SUM(DECODE(deptno, 20, sal)),0)) deptno20
    , MAX(NVL(SUM(DECODE(deptno, 30, sal)),0)) deptno30
    , MAX(NVL(SUM(DECODE(deptno, 40, sal)),0)) deptno40
FROM
    emp
GROUP BY
    deptno
```

* 결과

```text
DEPTNO10  DEPTNO20  DEPTNO30  DEPTNO40
-------- --------- --------- ---------
    8750     10875      9400         0
```

## [출처 및 참고]
* [https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions040.htm](https://docs.oracle.com/cd/B19306_01/server.102/b14200/functions040.htm)
* [https://www.gurubee.net/lecture/1028](https://www.gurubee.net/lecture/1028)
