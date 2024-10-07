---
title: JPA Pagination
author: dejavuhyo
date: 2024-10-07 11:13:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-pagination, pagination, jpa-페이징, 페이징, 페이지-매김]
---

## 1. JQL 및 setFirstResult(), setMaxResults() API를 사용한 페이지 매김
페이지 매김을 구현하는 가장 간단한 방법은 Java 쿼리 언어를 사용하는 것이다. 즉, 쿼리를 만들고 setMaxResults 및 setFirstResult를 통해 구성한다.

```java
Query query = entityManager.createQuery("From Foo");
int pageNumber = 1;
int pageSize = 10;
query.setFirstResult((pageNumber-1) * pageSize); 
query.setMaxResults(pageSize);
List <Foo> fooList = query.getResultList();
```

API는 간단하다.

* setFirstResult(int) : 결과 집합에서 페이지 분할을 시작할 오프셋 위치를 설정한다.

* setMaxResults(int) : 페이지에 포함되어야 하는 엔터티의 최대 수를 설정한다.

### 1) 총 개수와 마지막 페이지
더욱 완벽한 페이지 매김 솔루션을 위해서는 전체 결과 수를 얻어야 한다.

```java
Query queryTotal = entityManager.createQuery
    ("Select count(f.id) from Foo f");
long countResult = (long)queryTotal.getSingleResult();
```

마지막 페이지를 계산하는 것도 매우 유용하다.

```java
int pageSize = 10;
int pageNumber = (int) ((countResult / pageSize) + 1);
```

결과 집합의 총 개수를 구하는 이러한 접근 방식에는 추가 쿼리(개수에 대한)가 필요하다.

## 2. 엔티티의 ID를 사용하여 JQL로 페이지 나누기
간단한 대체 페이징 전략은 먼저 전체 ID를 검색한 다음 이를 기반으로 전체 엔터티를 검색하는 것이다. 이를 통해 엔터티 페칭을 더 잘 제어할 수 있지만 ID를 검색하려면 전체 테이블을 로드해야 한다.

```java
Query queryForIds = entityManager.createQuery("Select f.id from Foo f order by f.lastName");
List<Integer> fooIds = queryForIds.getResultList();
Query query = entityManager.createQuery("Select f from Foo e where f.id in :ids");
query.setParameter("ids", fooIds.subList(0,10));
List<Foo> fooList = query.getResultList();
```

마지막으로, 전체 결과를 검색하려면 2개의 서로 다른 쿼리가 필요하다.

## 3. Criteria API를 사용한 JPA로 페이지 나누기
JPA Criteria API를 활용하여 페이지 분할을 구현하는 방법이다.

```java
int pageSize = 10;
CriteriaBuilder criteriaBuilder = entityManager.getCriteriaBuilder();
CriteriaQuery<Foo> criteriaQuery = criteriaBuilder.createQuery(Foo.class);
Root<Foo> from = criteriaQuery.from(Foo.class);
CriteriaQuery<Foo> select = criteriaQuery.select(from);
TypedQuery<Foo> typedQuery = entityManager.createQuery(select);
typedQuery.setFirstResult(0);
typedQuery.setMaxResults(pageSize);
List<Foo> fooList = typedQuery.getResultList();
```

이것은 동적이고 실패에 안전한 쿼리를 만드는 것이 목표일 때 유용하다. "하드 코딩된", "문자열 기반" JQL 또는 HQL 쿼리와 달리 JPA Criteria는 컴파일러가 쿼리 오류를 동적으로 검사하기 때문에 런타임 실패를 줄인다.

JPA Criteria를 사용하면 매우 간단하게 엔터티의 총 수를 얻을 수 있다.

```java
CriteriaQuery<Long> countQuery = criteriaBuilder.createQuery(Long.class);
countQuery.select(criteriaBuilder.count(countQuery.from(Foo.class)));
Long count = entityManager.createQuery(countQuery).getSingleResult();
```

최종 결과는 JPA Criteria API를 사용한 전체 페이지 매김 솔루션 이다.

```java
int pageNumber = 1;
int pageSize = 10;
CriteriaBuilder criteriaBuilder = entityManager.getCriteriaBuilder();

CriteriaQuery<Long> countQuery = criteriaBuilder.createQuery(Long.class);
countQuery.select(criteriaBuilder.count(countQuery.from(Foo.class)));
Long count = entityManager.createQuery(countQuery).getSingleResult();

CriteriaQuery<Foo> criteriaQuery = criteriaBuilder.createQuery(Foo.class);
Root<Foo> from = criteriaQuery.from(Foo.class);
CriteriaQuery<Foo> select = criteriaQuery.select(from);

TypedQuery<Foo> typedQuery = entityManager.createQuery(select);
while (pageNumber < count.intValue()) {
    typedQuery.setFirstResult(pageNumber - 1);
    typedQuery.setMaxResults(pageSize);
    System.out.println("Current page: " + typedQuery.getResultList());
    pageNumber += pageSize;
}
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-pagination](https://www.baeldung.com/jpa-pagination)
