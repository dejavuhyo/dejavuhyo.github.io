---
title: JPA 단일 엔터티 여러 테이블 매핑
author: dejavuhyo
date: 2024-07-22 08:38:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-Entity, mapping-entity, tables-mapping, 엔터티-테이블-매핑, 테이블-매핑]
---

## 1. 테이블에 매핑
JPA는 Java 애플리케이션에서 관계형 데이터베이스 모델을 다루는 것을 덜 고통스럽게 만든다. 모든 테이블을 단일 엔티티 클래스에 매핑하면 모든 것이 간단하다.

하지만 때때로 엔터티와 테이블을 다르게 모델링해야 하는 이유가 있다.

* 필드의 논리적 그룹을 만들려면 여러 클래스를 단일 테이블에 매핑할 수 있다.

* 상속이 관련된 경우 클래스 계층 구조를 테이블 구조에 매핑할 수 있다.

* 관련 필드가 여러 테이블에 분산되어 있고 해당 테이블을 단일 클래스로 모델링하려는 경우이다.

## 2. 데이터 모델
예를 들어, 레스토랑을 운영한다고 가정하고, 제공하는 모든 식사에 대한 데이터를 저장하고 싶다고 한다.

* 이름

* 설명

* 가격

* 어떤 종류의 알레르기 물질이 포함되어 있는지

가능한 알레르기 유발 물질이 많으므로 이 데이터 세트를 그룹화한다.

또한 다음 테이블 정의를 사용하여 이를 모델링한다.

![meals](/assets/img/2024-07-22-jpa-mapping-entity-tables/meals.png)

## 3. 여러 엔터티 생성
가장 확실한 해결책은 두 클래스에 대한 엔터티를 만드는 것이다.

MealWithMultipleEntities 엔터티를 정의한다.

```java
@Entity
@Table(name = "meal")
public class MealWithMultipleEntities {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    Long id;

    @Column(name = "name")
    String name;

    @Column(name = "description")
    String description;

    @Column(name = "price")
    BigDecimal price;

    @OneToOne(mappedBy = "meal")
    AllergensAsEntity allergens;

    // standard getters and setters
}
```

다음으로 AllergensAsEntity 엔터티를 추가한다.

```java
@Entity
@Table(name = "allergens")
class AllergensAsEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "meal_id")
    Long mealId;

    @OneToOne
    @PrimaryKeyJoinColumn(name = "meal_id")
    Meal meal;

    @Column(name = "peanuts")
    boolean peanuts;

    @Column(name = "celery")
    boolean celery;

    @Column(name = "sesame_seeds")
    boolean sesameSeeds;

    // standard getters and setters
}
```

meal_id가 기본 키이자 외래 키라는 것을 알 수 있다. 즉, `@PrimaryKeyJoinColumn`을 사용하여 일대일 관계 열을 정의해야 한다.

하지만 이 방법에는 두 가지 문제가 있다.

* 항상 식사를 위해 알레르기 유발 물질을 저장하고 싶어하는데, 이 방법은 이 규칙을 강제하지 않는다.

* 식사와 알레르기 데이터는 논리적으로 함께 속한다. 따라서 여러 테이블을 만들었더라도 이 정보를 동일한 Java 클래스에 저장하고 싶을 수 있다.

첫 번째 문제에 대한 가능한 해결책 중 하나는 MealWithMultipleEntities 엔터티의 allergens 필드에 `@NotNull` 주석을 추가하는 것이다. JPA는 null AllergensAsEntity가 있는 경우 MealWithMultipleEntities를 지속할 수 없다.

하지만 이것은 이상적인 해결책이 아니다. AllergensAsEntity 없이 MealWithMultipleEntities를 지속할 기회조차 없는 더 제한적인 해결책이 필요하다.

## 4. @SecondaryTable로 단일 엔터티 생성
`@SecondaryTable` 주석을 사용하면 서로 다른 테이블에 열이 있음을 지정하여 단일 엔터티를 생성할 수 있다.

```java
@Entity
@Table(name = "meal")
@SecondaryTable(name = "allergens", pkJoinColumns = @PrimaryKeyJoinColumn(name = "meal_id"))
class MealAsSingleEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    Long id;

    @Column(name = "name")
    String name;

    @Column(name = "description")
    String description;

    @Column(name = "price")
    BigDecimal price;

    @Column(name = "peanuts", table = "allergens")
    boolean peanuts;

    @Column(name = "celery", table = "allergens")
    boolean celery;

    @Column(name = "sesame_seeds", table = "allergens")
    boolean sesameSeeds;

    // standard getters and setters

}
```

백그라운드에서 JPA는 기본 테이블과 보조 테이블을 조인하고 필드를 채운다. 이 방법은 `@OneToOne` 관계와 비슷 하지만, 이런 방식으로 모든 속성을 같은 클래스에 둘 수 있다.

2차 테이블에 있는 열이 있는 경우 `@Column` 주석의 table 인수로 지정해야 한다. 1차 테이블에 있는 열의 경우 JPA가 기본적으로 1차 테이블에서 열을 찾기 때문에 table 인수를 생략할 수 있다.

또한, `@SecondaryTables`에 임베드하면 여러 개의 보조 테이블을 가질 수 있다. 또는 Java 8부터 반복 가능한 주석이기 때문에 여러 개의 `@SecondaryTable` 주석 으로 엔티티를 표시할 수 있다.

## 5. @SecondaryTable과 @Embedded 결합
`@SecondaryTable`은 여러 테이블을 동일한 엔터티에 매핑한다. 또한 `@Embedded` 와 `@Embeddable`은 반대로 단일 테이블을 여러 클래스에 매핑한다.

`@SecondaryTable`과 `@Embedded`, `@Embeddable`을 결합하면 어떤 결과가 나오는지 확인한다.

```java
@Entity
@Table(name = "meal")
@SecondaryTable(name = "allergens", pkJoinColumns = @PrimaryKeyJoinColumn(name = "meal_id"))
class MealWithEmbeddedAllergens {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    Long id;

    @Column(name = "name")
    String name;

    @Column(name = "description")
    String description;

    @Column(name = "price")
    BigDecimal price;

    @Embedded
    AllergensAsEmbeddable allergens;

    // standard getters and setters

}

@Embeddable
class AllergensAsEmbeddable {

    @Column(name = "peanuts", table = "allergens")
    boolean peanuts;

    @Column(name = "celery", table = "allergens")
    boolean celery;

    @Column(name = "sesame_seeds", table = "allergens")
    boolean sesameSeeds;

    // standard getters and setters

}
```

이는 `@OneToOne`을 사용하여 본 것과 유사한 접근 방식이다. 그러나 몇 가지 장점이 있다.

* JPA가 두 테이블을 함께 관리해주기 때문에 두 테이블 모두 각 식사마다 행이 있도록 할 수 있다.

* 구성이 덜 필요하므로 코드도 조금 더 간단해졌다.

그럼에도 불구하고, 이러한 일대일 유사 솔루션은 두 테이블의 ID가 일치하는 경우에만 작동한다.

AllergensAsEmbeddable 클래스를 재사용하려면 MealWithEmbeddedAllergens 클래스에서 보조 테이블의 열을 `@AttributeOverride`로 정의하는 것이 더 좋다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-mapping-single-entity-to-multiple-tables](https://www.baeldung.com/jpa-mapping-single-entity-to-multiple-tables)
