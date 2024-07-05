---
title: Hibernate @NotNull와 @Column(nullable = false)
author: dejavuhyo
date: 2024-07-04 10:44:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate-notnull, hibernate-nullable, notnull-nullable]
---

## 1. 종속성
예제에서 Spring Boot 애플리케이션을 사용한다.

다음은 필요한 종속성을 보여주는 `pom.xml` 파일이다.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
    </dependency>
</dependencies>
```

### 1) 샘플 엔티티
전체에서 사용할 매우 간단한 엔터티 정의이다.

```java
@Entity
public class Item {

    @Id
    @GeneratedValue
    private Long id;

    private BigDecimal price;
}
```

## 2. @NotNull 주석
`@NotNull` 주석은 Bean Validation 사양에 정의되어 있다. 즉, 엔티티에만 사용이 제한되지 않는다. 반대로, 다른 모든 Bean에서도 `@NotNull`을 사용할 수 있다.

Item의 가격 필드에 `@NotNull` 주석을 추가한다.

```java
@Entity
public class Item {

    @Id
    @GeneratedValue
    private Long id;

    @NotNull
    private BigDecimal price;
}
```

이제 null 가격이 있는 항목을 지속해본다.

```java
@SpringBootTest
public class ItemIntegrationTest {

    @Autowired
    private ItemRepository itemRepository;

    @Test
    public void shouldNotAllowToPersistNullItemsPrice() {
        itemRepository.save(new Item());
    }
}
```

Hibernate의 출력을 확인한다.

```java
2019-11-14 12:31:15.070 ERROR 10980 --- [ main] o.h.i.ExceptionMapperStandardImpl : 
HHH000346: Error during managed flush [Validation failed for classes 
[com.baeldung.h2db.springboot.models.Item] during persist time for groups 
[javax.validation.groups.Default,] List of constraint violations:[
ConstraintViolationImpl{interpolatedMessage='must not be null', propertyPath=price, rootBeanClass=class 
com.baeldung.h2db.springboot.models.Item, 
messageTemplate='{javax.validation.constraints.NotNull.message}'}]]
 
(...)
 
Caused by: javax.validation.ConstraintViolationException: Validation failed for classes 
[com.baeldung.h2db.springboot.models.Item] during persist time for groups 
[javax.validation.groups.Default,] List of constraint violations:[
ConstraintViolationImpl{interpolatedMessage='must not be null', propertyPath=price, rootBeanClass=class 
com.baeldung.h2db.springboot.models.Item, 
messageTemplate='{javax.validation.constraints.NotNull.message}'}]
```

보시다시피 이 경우 시스템은 `javax.validation.ConstraintViolationException`을 throw했다.

Hibernate가 SQL insert 문을 트리거하지 않았다. 결과적으로 잘못된 데이터가 데이터베이스에 저장되지 않았다.

이는 사전 지속형 엔터티 수명 주기 이벤트가 데이터베이스로 쿼리를 보내기 직전에 빈 검증을 트리거했기 때문이다.

### 1) 스키마 생성
`@NotNull` 검증이 어떻게 작동하는지 확인했다.

Hibernate가 데이터베이스 스키마를 생성하게 하면 어떤 일이 일어나는지 확인한다.

`application.properties` 파일에 몇 가지 속성을 설정한다.

```properties
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
```

이제 애플리케이션을 시작하면 DDL 문이 표시된다.

```sql
create table item (
   id bigint not null,
    price decimal(19,2) not null,
    primary key (id)
)
```

Hibernate는 자동으로 price 열 정의에 not null 제약 조건을 추가한다.

실제로 Hibernate는 엔터티에 적용된 빈 검증 주석을 DDL 스키마 메타데이터로 변환한다.

이것은 매우 편리하고 매우 합리적이다. 엔티티에 `@NotNull`을 적용하면 해당 데이터베이스 열도 null이 아니게 만들고 싶을 것이다.

하지만 어떤 이유로든 Hibernate 기능을 비활성화하고 싶다면 `hibernate.validator.apply_to_ddl` 속성을 false로 설정하기만 하면 된다.

이를 테스트하기 위해 `application.properties`를 업데이트해 본다.

```properties
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.validator.apply_to_ddl=false
```

애플리케이션을 실행하고 DDL 문을 살펴본다.

```sql
create table item (
   id bigint not null,
    price decimal(19,2),
    primary key (id)
)
```

예상대로 이번에 Hibernate는 price 열에 not null 제약 조건을 추가하지 않았다.

## 3. @Column(nullable = false) 주석
`@Column` 주석은 Java Persistence API 사양의 일부로 정의된다.

주로 DDL 스키마 메타데이터 생성에 사용된다. 즉, Hibernate가 데이터베이스 스키마를 자동으로 생성하게 하면 특정 데이터베이스 열에 not null 제약 조건이 적용된다.

`@Column(nullable = false)`로 Item 엔터티를 업데이트하고 이것이 실제로 어떻게 작동하는지 확인한다.

```java
@Entity
public class Item {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private BigDecimal price;
}
```

이제 null 가격 값을 유지하려고 시도할 수 있다.

```java
@SpringBootTest
public class ItemIntegrationTest {

    @Autowired
    private ItemRepository itemRepository;

    @Test
    public void shouldNotAllowToPersistNullItemsPrice() {
        itemRepository.save(new Item());
    }
}
```

Hibernate 출력의 일부이다.

```text
Hibernate: 
    
    create table item (
       id bigint not null,
        price decimal(19,2) not null,
        primary key (id)
    )

(...)

Hibernate: 
    insert 
    into
        item
        (price, id) 
    values
        (?, ?)
2019-11-14 13:23:03.000  WARN 14580 --- [main] o.h.engine.jdbc.spi.SqlExceptionHelper   : 
SQL Error: 23502, SQLState: 23502
2019-11-14 13:23:03.000 ERROR 14580 --- [main] o.h.engine.jdbc.spi.SqlExceptionHelper   : 
NULL not allowed for column "PRICE"
```

예상했던 대로 Hibernate가 not null 제약 조건을 사용하여 price 열을 생성했다.

또한 SQL 삽입 쿼리를 생성하여 통과시킬 수 있었다. 결과적으로 오류를 트리거한 것은 기본 데이터베이스였다.

### 1) 검증
거의 모든 소스에서 `@Column(nullable = false)`는 스키마 DDL 생성에만 사용된다고 강조한다.

그러나 Hibernate는 해당 필드에 `@Column(nullable = false)` 주석이 달려 있는 경우에도 가능한 null 값에 대해 엔터티의 유효성 검사를 수행 할 수 있다.

이 Hibernate 기능을 활성화하려면 `hibernate.check_nullability` 속성을 명시적으로 true로 설정해야 한다.

```properties
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.check_nullability=true
```

이제 테스트 케이스를 다시 실행하고 출력을 확인한다.

```text
org.springframework.dao.DataIntegrityViolationException: 
not-null property references a null or transient value : com.baeldung.h2db.springboot.models.Item.price; 
nested exception is org.hibernate.PropertyValueException: 
not-null property references a null or transient value : com.baeldung.h2db.springboot.models.Item.price
```

`org.hibernate.PropertyValueException`을 throw 했다.

이 경우 Hibernate가 데이터베이스에 삽입 SQL 쿼리를 전송하지 않았다.

## [출처 및 참고]
* [https://www.baeldung.com/hibernate-notnull-vs-nullable](https://www.baeldung.com/hibernate-notnull-vs-nullable)
