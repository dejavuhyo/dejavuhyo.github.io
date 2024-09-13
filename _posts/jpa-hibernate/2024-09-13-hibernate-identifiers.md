---
title: Hibernate 식별자
author: dejavuhyo
date: 2024-09-13 08:45:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [hibernate-identifiers, hibernate, jpa, entity-id, 하이버네이트-식별자]
---

## 1. 간단한 식별자
식별자를 정의하는 가장 간단한 방법은 `@Id` 주석을 사용하는 것이다.

간단한 ID는 `@Id`를 사용하여 다음 유형 중 하나의 단일 속성에 매핑된다. Java 기본 및 기본 래퍼 유형, String, Date, BigDecimal 및 BigInteger.

long 유형의 기본 키를 사용하여 엔터티를 정의하는 간단한 예이다.

```java
@Entity
public class Student {

    @Id
    private long studentId;
    
    // standard constructor, getters, setters
}
```

## 2. 생성된 식별자
기본 키 값을 자동으로 생성하려면 `@GeneratedValue` 주석을 추가할 수 있다.

`AUTO`, `IDENTITY`, `SEQUENCE`, `TABLE`의 4가지 생성 유형을 사용할 수 있다.

값을 명시적으로 지정하지 않으면 생성 유형은 기본적으로 AUTO로 설정된다.

### 1) AUTO 생성
기본 생성 유형을 사용하는 경우 지속성 공급자는 기본 키 속성의 유형에 따라 값을 결정한다. 이 유형은 숫자형 또는 UUID 일 수 있다.

숫자 값의 경우 시퀀스 또는 테이블 생성기를 기반으로 생성되는 반면, UUID 값은 UUIDGenerator를 사용한다.

먼저 AUTO 생성 전략을 사용하여 엔터티 기본 키를 매핑한다.

```java
@Entity
public class Student {

    @Id
    @GeneratedValue
    private long studentId;

    // ...
}
```

이 경우 기본 키 값은 데이터베이스 수준에서 고유하다.

이제 Hibernate 5에서 도입된 UUIDGenerator를 살펴본다.

이 기능을 사용하려면 `@GeneratedValue` 주석을 사용하여 UUID 유형의 ID를 선언하기만 하면 된다.

```java
@Entity
public class Course {

    @Id
    @GeneratedValue
    private UUID courseId;

    // ...
}
```

Hibernate는 `8dd5f315-9788-4d00-87bb-10eed9eff566` 형태의 ID를 생성한다.

### 2) IDENTITY 생성
이 유형의 생성은 데이터베이스의 ID 열에서 생성된 값을 예상하는 IdentityGenerator에 의존한다. 즉, 값이 자동으로 증가한다.

이 세대 유형을 사용하려면 전략 매개변수만 설정하면 된다.

```java
@Entity
public class Student {

    @Id
    @GeneratedValue (strategy = GenerationType.IDENTITY)
    private long studentId;

    // ...
}
```

주의할 점은 IDENTITY 생성을 통해 일괄 업데이트가 비활성화된다는 것이다.

### 3) SEQUENCE 생성
시퀀스 기반 ID를 사용하기 위해 Hibernate는 SequenceStyleGenerator 클래스를 제공한다.

이 생성기는 데이터베이스가 지원하면 시퀀스를 사용한다. 지원되지 않으면 테이블 생성으로 전환한다.

시퀀스 이름을 사용자 정의하려면 SequenceStyleGenerator 전략과 함께 `@GenericGenerator` 주석을 사용할 수 있다.

```java
@Entity
public class User {
    @Id
    @GeneratedValue(generator = "sequence-generator")
    @GenericGenerator(
      name = "sequence-generator",
      strategy = "org.hibernate.id.enhanced.SequenceStyleGenerator",
      parameters = {
        @Parameter(name = "sequence_name", value = "user_sequence"),
        @Parameter(name = "initial_value", value = "4"),
        @Parameter(name = "increment_size", value = "1")
        }
    )
    private long userId;
    
    // ...
}
```

이 예에서 시퀀스에 대한 초기 값도 설정했다. 즉, 기본 키 생성은 4에서 시작된다.

SEQUENCE는 Hibernate 설명서에서 권장하는 생성 유형이다.

생성된 값은 시퀀스마다 고유하다. 시퀀스 이름을 지정하지 않으면 Hibernate는 다른 유형에 대해 동일한 hibernate_sequence를 재사용한다.

### 4) TABLE 생성
TableGenerator는 식별자 생성 값의 세그먼트를 보관하는 기본 데이터베이스 테이블을 사용한다.

`@TableGenerator` 주석을 사용하여 테이블 이름을 사용자 지정한다.

```java
@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.TABLE, 
      generator = "table-generator")
    @TableGenerator(name = "table-generator", 
      table = "dep_ids", 
      pkColumnName = "seq_id", 
      valueColumnName = "seq_value")
    private long depId;

    // ...
}
```

이 예에서는 pkColumnName 및 valueColumnName과 같은 다른 속성도 사용자 정의할 수 있다.

그러나 이 방법의 단점은 확장성이 떨어지고 성능에 부정적인 영향을 미칠 수 있다는 것이다.

요약하면, 이 네 가지 생성 유형은 비슷한 값을 생성하지만 서로 다른 데이터베이스 메커니즘을 사용한다.

### 5) 사용자 정의 생성기
어떤 기본 전략도 사용하고 싶지 않다고 가정한다. 그렇게 하려면 IdentifierGenerator 인터페이스를 구현하여 사용자 지정 생성기를 정의할 수 있다.

문자열 접두사와 숫자를 포함하는 식별자를 작성하는 생성기를 만든다.

```java
public class MyGenerator 
  implements IdentifierGenerator, Configurable {

    private String prefix;

    @Override
    public Serializable generate(
      SharedSessionContractImplementor session, Object obj) 
      throws HibernateException {

        String query = String.format("select %s from %s", 
            session.getEntityPersister(obj.getClass().getName(), obj)
              .getIdentifierPropertyName(),
            obj.getClass().getSimpleName());

        Stream ids = session.createQuery(query).stream();

        Long max = ids.map(o -> o.replace(prefix + "-", ""))
          .mapToLong(Long::parseLong)
          .max()
          .orElse(0L);

        return prefix + "-" + (max + 1);
    }

    @Override
    public void configure(Type type, Properties properties, 
      ServiceRegistry serviceRegistry) throws MappingException {
        prefix = properties.getProperty("prefix");
    }
}
```

예제에서는 IdentifierGenerator 인터페이스의 `generate()` 메서드를 재정의한다.

먼저, `prefix-XX` 형태의 기존 기본 키에서 가장 높은 숫자를 찾고 싶다. 그런 다음 찾은 최대 숫자에 1을 더하고 prefix 속성을 추가하여 새로 생성된 id 값을 얻는다.

클래스는 Configurable 인터페이스도 구현해서 `configure()` 메서드에서 prefix 속성 값을 설정할 수 있다.

다음으로, 이 사용자 지정 생성기를 엔터티에 추가한다.

이를 위해, 생성기 클래스의 전체 클래스 이름을 포함하는 전략 매개변수와 함께 `@GenericGenerator` 주석을 사용할 수 있다.

```java
@Entity
public class Product {

    @Id
    @GeneratedValue(generator = "prod-generator")
    @GenericGenerator(name = "prod-generator", 
      parameters = @Parameter(name = "prefix", value = "prod"), 
      strategy = "com.baeldung.hibernate.pojo.generator.MyGenerator")
    private String prodId;

    // ...
}
```

또한, 접두사 매개변수를 "prod"로 설정했다.

생성된 id 값을 더 명확하게 이해하기 위해 간단한 JUnit 테스트이다.

```java
@Test
public void whenSaveCustomGeneratedId_thenOk() {
    Product product = new Product();
    session.save(product);
    Product product2 = new Product();
    session.save(product2);

    assertThat(product2.getProdId()).isEqualTo("prod-2");
}
```

여기서 "prod" 접두사를 사용하여 생성된 첫 번째 값은 "prod-1"이고 그 다음은 "prod-2"이다.

## 3. 복합 식별자
식별자 외에도 Hibernate에서는 복합 식별자를 정의할 수도 있다.

복합 ID는 하나 이상의 영구 속성을 갖는 기본 키 클래스로 표현된다.

기본 키 클래스는 여러 조건을 충족해야 한다.

* `@EmbeddedId` 또는 `@IdClass` 주석을 사용하여 정의해야 한다.

* 공개적이고 직렬화 가능해야 하며 인수 없는 공개 생성자가 있어야 한다.\

* `equals()` 및 `hashCode()` 메서드를 구현해야 한다.

클래스의 속성은 기본, 합성 또는 ManyToOne이 될 수 있으며, 컬렉션 및 OneToOne 속성은 사용할 수 없다.

### 1) @EmbeddedId
`@EmbeddedId`를 사용하여 ID를 정의하는 방법이다.

먼저, `@Embeddable` 주석이 달린 기본 키 클래스가 필요하다.

```java
@Embeddable
public class OrderEntryPK implements Serializable {

    private long orderId;
    private long productId;

    // standard constructor, getters, setters
    // equals() and hashCode() 
}
```

다음으로, `@EmbeddedId`를 사용하여 엔터티에 OrderEntryPK 유형의 ID를 추가할 수 있다.

```java
@Entity
public class OrderEntry {

    @EmbeddedId
    private OrderEntryPK entryId;

    // ...
}
```

이 유형의 복합 ID를 사용하여 엔터티의 기본 키를 설정하는 방법이다.

```java
@Test
public void whenSaveCompositeIdEntity_thenOk() {
    OrderEntryPK entryPK = new OrderEntryPK();
    entryPK.setOrderId(1L);
    entryPK.setProductId(30L);
        
    OrderEntry entry = new OrderEntry();
    entry.setEntryId(entryPK);
    session.save(entry);

    assertThat(entry.getEntryId().getOrderId()).isEqualTo(1L);
}
```

여기서 OrderEntry 객체는 orderId와 productId라는 두 가지 속성으로 구성된 OrderEntryPK 기본 ID를 갖는다.

### 2) @IdClass
`@IdClass` 주석은 `@EmbeddedId`와 유사하다. `@IdClass`와의 차이점은 속성이 각각에 대해 `@Id`를 사용하여 주 엔터티 클래스에서 정의된다는 것이다. 기본 키 클래스는 이전과 동일하게 보인다.

`@IdClass`를 사용하여 OrderEntry 예제를 다시 작성한다.

```java
@Entity
@IdClass(OrderEntryPK.class)
public class OrderEntry {
    @Id
    private long orderId;
    @Id
    private long productId;
    
    // ...
}
```

그런 다음 OrderEntry 객체에 직접 id 값을 설정할 수 있다.

```
@Test
public void whenSaveIdClassEntity_thenOk() {
    OrderEntry entry = new OrderEntry();
    entry.setOrderId(1L);
    entry.setProductId(30L);
    session.save(entry);

    assertThat(entry.getOrderId()).isEqualTo(1L);
}
```

두 가지 유형의 복합 ID 모두 기본 키 클래스에 `@ManyToOne` 속성도 포함될 수 있다.

```java
@Embeddable
public class OrderEntryPK implements Serializable {

    private long orderId;
    private long productId;
    @ManyToOne
    private User user;

    // ...
}
```

```java
@Entity
@IdClass(OrderEntryPK.class)
public class OrderEntryIdClass {
    @Id
    private long orderId;
    @Id
    private long productId;
    @ManyToOne
    private User user;

    // ...
}
```

Hibernate는 또한 `@ManyToOne` 연관과 `@Id` 주석을 결합한 기본 키를 정의하는 것을 허용한다. 이 경우 엔티티 클래스는 기본 키 클래스의 조건도 충족해야 한다.

하지만 이 방법의 단점은 엔티티 객체와 식별자가 분리되지 않는다는 것이다.

## 4. 파생 식별자
파생된 식별자는 `@MapsId` 주석을 사용하여 엔터티의 연결에서 가져온다.

먼저, User 엔터티 와의 일대일 연결에서 ID를 파생하는 UserProfile 엔터티를 만든다.

```java
@Entity
public class UserProfile {

    @Id
    private long profileId;
    
    @OneToOne
    @MapsId
    private User user;

    // ...
}
```

다음으로, UserProfile 인스턴스가 연관된 User 인스턴스와 동일한 ID를 가지고 있는지 확인한다.

```java
@Test
public void whenSaveDerivedIdEntity_thenOk() {
    User user = new User();
    session.save(user);
       
    UserProfile profile = new UserProfile();
    profile.setUser(user);
    session.save(profile);

    assertThat(profile.getProfileId()).isEqualTo(user.getUserId());
}
```

## [출처 및 참고]
* [https://www.baeldung.com/hibernate-identifiers](https://www.baeldung.com/hibernate-identifiers)
