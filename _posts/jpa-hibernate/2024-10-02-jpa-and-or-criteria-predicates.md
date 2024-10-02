---
title: JPA And/Or 기준 조건 결합
author: dejavuhyo
date: 2024-10-02 08:30:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-and-or, jpa-and, jpa-or, jpa-criteria-predicates]
---

## 1. 샘플 애플리케이션
예를 들어, 각각 id, name, color, grade를 갖는 Item 엔터티가 있다.

```java
@Entity
public class Item {

    @Id
    private Long id;
    private String color;
    private String grade;
    private String name;
    
    // standard getters and setters
}
```

## 2. AND 술어를 사용하여 두 개의 OR 술어 결합
다음 두 가지를 모두 포함하는 항목을 찾아야 하는 시나리오를 생각해 보겠습니다.

* 빨간색 또는 파란색

AND

* A 또는 B 등급

JPA Criteria API의 `and()`와 `or()` 복합 조건을 사용하면 이 작업을 쉽게 수행할 수 있다.

쿼리를 다음과 같이 설정한다.

```java
CriteriaBuilder criteriaBuilder = entityManager.getCriteriaBuilder();
CriteriaQuery<Item> criteriaQuery = criteriaBuilder.createQuery(Item.class);
Root<Item> itemRoot = criteriaQuery.from(Item.class);
```

파란색 또는 빨간색 색상을 갖는 항목을 찾기 위한 Predicate를 작성한다.

```java
Predicate predicateForBlueColor = criteriaBuilder.equal(itemRoot.get("color"), "blue");
Predicate predicateForRedColor = criteriaBuilder.equal(itemRoot.get("color"), "red");
Predicate predicateForColor = criteriaBuilder.or(predicateForBlueColor, predicateForRedColor);
```

A 또는 B 등급의 항목을 찾기 위한 술어를 작성한다.

```java
Predicate predicateForGradeA = criteriaBuilder.equal(itemRoot.get("grade"), "A");
Predicate predicateForGradeB = criteriaBuilder.equal(itemRoot.get("grade"), "B");
Predicate predicateForGrade = criteriaBuilder.or(predicateForGradeA, predicateForGradeB);
```

이 둘의 AND 술어를 정의하고 `where()` 메서드를 적용한 다음 쿼리를 실행한다.

```java
Predicate finalPredicate = criteriaBuilder.and(predicateForColor, predicateForGrade);
criteriaQuery.where(finalPredicate);
List<Item> items = entityManager.createQuery(criteriaQuery).getResultList();
```

## 3. OR 술어를 사용하여 두 개의 AND 술어 결합
반대로, 다음 중 하나를 갖는 항목을 찾아야 하는 시나리오가 있다.

* 빨간색과 D등급

OR

* 파란색과 B등급

논리는 매우 유사하지만 여기서는 먼저 두 개의 AND Predicate를 생성한 다음 OR Predicate를 사용하여 두 개를 결합한다.

```java
CriteriaBuilder criteriaBuilder = entityManager.getCriteriaBuilder();
CriteriaQuery<Item> criteriaQuery = criteriaBuilder.createQuery(Item.class);
Root<Item> itemRoot = criteriaQuery.from(Item.class);

Predicate predicateForBlueColor = criteriaBuilder.equal(itemRoot.get("color"), "red");
Predicate predicateForGradeA = criteriaBuilder.equal(itemRoot.get("grade"), "D");
Predicate predicateForBlueColorAndGradeA = criteriaBuilder.and(predicateForBlueColor, predicateForGradeA);

Predicate predicateForRedColor = criteriaBuilder.equal(itemRoot.get("color"), "blue");
Predicate predicateForGradeB = criteriaBuilder.equal(itemRoot.get("grade"), "B");
Predicate predicateForRedColorAndGradeB = criteriaBuilder.and(predicateForRedColor, predicateForGradeB);

Predicate finalPredicate = criteriaBuilder
  .or(predicateForBlueColorAndGradeA, predicateForRedColorAndGradeB);
criteriaQuery.where(finalPredicate);
List<Item> items = entityManager.createQuery(criteriaQuery).getResultList();
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-and-or-criteria-predicates](https://www.baeldung.com/jpa-and-or-criteria-predicates)
