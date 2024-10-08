---
title: Hibernate Pagination
author: dejavuhyo
date: 2024-10-08 17:30:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate-pagination, pagination, hibernate-페이징, 페이징, 페이지-매김]
---

## 1. HQL 및 setFirstResult, setMaxResults API를 사용한 페이징
Hibernate에서 페이징을 하는 가장 간단하고 일반적인 방법은 HQL을 사용하는 것이다.

```java
Session session = sessionFactory.openSession();
Query<Foo> query = session.createQuery("From Foo", Foo.class);
query.setFirstResult(0);
query.setMaxResults(10);
List<Foo> fooList = fooList = query.list();
```

이 예제는 기본 Foo 엔티티를 사용하고 있으며 JQL 구현을 사용한 JPA와 매우 유사하다. 유일한 차이점은 쿼리 언어이다.

Hibernate에 대한 로깅을 켜면 다음 SQL이 실행되는 것을 볼 수 있다.

```text
Hibernate:
    select
        foo0_.id as id1_1_,
        foo0_.name as name2_1_
    from
        Foo foo0_ limit ?
```

### 1) 총 개수와 마지막 페이지
엔터티의 총 개수를 알지 못하면 페이지 번호 매기기 솔루션이 완전하지 않다.

```java
String countQ = "Select count (f.id) from Foo f";
Query<Long> countQuery = session.createQuery(countQ, Long.class);
Long countResults = countQuery.uniqueResult();
```

마지막으로, 총 페이지 수와 주어진 페이지 크기에서 마지막 페이지를 계산할 수 있다.

```java
int pageSize = 10;
int lastPageNumber = (int) (Math.ceil(countResults / pageSize));
```

이 시점에서 페이징에 대한 완전한 예를 살펴볼 수 있다. 여기서 마지막 페이지를 계산한 다음 검색한다.

```java
@Test
public void givenEntitiesExist_whenRetrievingLastPage_thenCorrectSize() {
    int pageSize = 10;
    String countQ = "Select count (f.id) from Foo f";
    Query<Long> countQuery = session.createQuery(countQ, Long.class);
    Long countResults = (Long) countQuery.uniqueResult();
    int lastPageNumber = (int) (Math.ceil(countResults / pageSize));

    Query<Foo> selectQuery = session.createQuery("From Foo", Foo.class);
    selectQuery.setFirstResult((lastPageNumber - 1) * pageSize);
    selectQuery.setMaxResults(pageSize);
    List<Foo> lastPage = selectQuery.list();

    assertThat(lastPage, hasSize(lessThan(pageSize + 1)));
}
```

## 2. HQL 및 ScrollableResults API를 사용한 Hibernate로 페이지 나누기
ScrollableResults를 사용하여 페이징을 구현하면 데이터베이스 호출을 줄일 수 있는 잠재력이 있다. 이 접근 방식은 프로그램이 스크롤할 때 결과 세트를 스트리밍하므로 각 페이지를 채우기 위해 쿼리를 반복할 필요가 없다.

```java
String hql = "FROM Foo f order by f.name";
Query query = session.createQuery(hql);
int pageSize = 10;

ScrollableResults resultScroll = query.scroll(ScrollMode.FORWARD_ONLY);
resultScroll.first();
resultScroll.scroll(0);
List<Foo> fooPage = Lists.newArrayList();
int i = 0;
while (pageSize > i++) {
    fooPage.add((Foo) resultScroll.get(0));
    if (!resultScroll.next())
        break;
}
```

이 방법은 시간 효율적일 뿐만 아니라(단 하나의 데이터베이스 호출만 필요함) 사용자가 추가 쿼리 없이 결과 세트의 총 개수에 액세스할 수 있게 해준다.

```java
resultScroll.last();
int totalResults = resultScroll.getRowNumber() + 1;
```

반면, 스크롤이 매우 효율적이기는 하지만, 큰 창은 상당한 양의 메모리를 차지할 수 있다.

## 3. Criteria API를 사용한 Hibernate로 페이징
기준을 사용한 보다 유연한 솔루션이다.

```java
CriteriaQuery<Foo> selectQuery = session.getCriteriaBuilder().createQuery(Foo.class);
selectQuery.from(Foo.class);
SelectionQuery<Foo> query = session.createQuery(selectQuery);
query.setFirstResult(0);
query.setMaxResults(pageSize);
List<Foo> firstPage = query.list();
```

Hibernate Criteria 쿼리 API를 사용하면 총 개수를 얻는 것도 매우 간단하다.

```java
HibernateCriteriaBuilder qb = session.getCriteriaBuilder();
CriteriaQuery<Long> cq = qb.createQuery(Long.class);
cq.select(qb.count(cq.from(Foo.class)));
final Long count = session.createQuery(cq).getSingleResult();
```

보시다시피, 이 API를 사용하면 일반 HQL보다 최소한 더 복잡한 코드가 생성되지만 API는 완전히 유형이 안전하고 훨씬 더 유연하다.

## [출처 및 참고]
* [https://www.baeldung.com/hibernate-pagination](https://www.baeldung.com/hibernate-pagination)
