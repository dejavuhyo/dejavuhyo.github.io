---
title: Hibernate 기본 키로 UUID 생성
author: dejavuhyo
date: 2024-09-23 08:35:00 +0900
categories: [Application, IDE]
tags: [hibernate-primary-key, primary-key, hibernate-key, hibernate-uuid, 하이버네이트-기본-키]
---

## 1. JPA/Jakarta 사양
2022년에 출시된 버전 3.1.0 이후 JPA 사양은 개발자에게 `@GeneratedValue` 주석에서 사용할 수 있는 새로운 `GenerationType.UUID`를 제공한다.

```java
@Entity
class Reservation {

    @Id
    @GeneratedValue(strategy = GenerationType.UUID)
    private UUID id;

    private String status;

    private String number;

    // getters and setters
}
```

GenerationType은 지속성 공급자가 엔터티의 UUID를 자동으로 생성해야 함을 지시한다.

특히 Hibernate는 버전 6.2부터 JPA 3.1.0을 지원한다. 따라서 최소한 Hibernate 6.2가 있다면 다음과 같이 작동한다.

```java
@Test
public void whenGeneratingUUIDUsingNewJPAGenerationType_thenHibernateGeneratedUUID() throws IOException {
    Reservation reservation = new Reservation();
    reservation.setStatus("created");
    reservation.setNumber("12345");
    UUID saved = (UUID) session.save(reservation);
    Assertions.assertThat(saved).isNotNull();
}
```

그러나 RFC 4122에는 정의된 UUID의 유형/버전이 4가지가 있다. 그리고 JPA 사양은 UUID 버전 선택을 지속성 공급자에게 맡깉다. 따라서 다른 지속성 공급자는 다른 버전의 UUID를 생성할 수 있다.

기본적으로 Hibernate는 4번째 버전의 UUID를 생성한다.

```java
@Test
public void whenGeneratingUUIDUsingNewJPAGenerationType_thenHibernateGeneratedUUIDOfVersion4() throws IOException {
    Reservation reservation = new Reservation();
    reservation.setStatus("new");
    reservation.setNumber("012");
    UUID saved = (UUID) session.save(reservation);
    Assertions.assertThat(saved).isNotNull();
    Assertions.assertThat(saved.version()).isEqualTo(4);
}
```

RFC 4122에 따르면 Hibernate는 1과 4의 두 가지 버전의 UUID를 생성할 수 있다.

## 2. Hibernate 6.2 이전
일부 프로젝트에서는 JPA 사양 2.x에서 JPA(또는 Jakarta) 사양 3.1.0으로 점프하는 것이 불가능할 수 있다. 그러나 Hibernate 버전 4 또는 5가 있는 경우 여전히 UUID를 생성할 수 있다. 이를 위해 두 가지 접근 방식이 있다.

첫째, `@GenericGenerator` 주석에서 `org.hibernate.id.UUIDGenerator` 클래스를 지정하여 이를 달성할 수 있다.

```java
@Entity
class Sale {

    @Id
    @GeneratedValue(generator = "uuid-hibernate-generator")
    @GenericGenerator(name = "uuid-hibernate-generator", strategy = "org.hibernate.id.UUIDGenerator")
    private UUID id;

    private boolean completed;

    //getters and setters
}
```

그리고 동작은 Hibernate 6.2와 동일하다.

```java
@Test
public void whenGeneratingUUIDUsingGenericConverter_thenAlsoGetUUIDGeneratedVersion4() throws IOException {
    Sale sale = new Sale();
    sale.setCompleted(true);
    UUID saved = (UUID) session.save(sale);
    Assertions.assertThat(saved).isNotNull();
    Assertions.assertThat(saved.version()).isEqualTo(4);
}
```

그러나 이 접근 방식은 매우 장황하며 `org.hibernate.annotations.UuidGenerator` 주석만 사용하여 동일한 동작을 얻을 수 있다.

```java
@Entity
class Sale {

    @Id
    @UuidGenerator
    private UUID id;

    private boolean completed;

    // getters and setters 
}
```

게다가 `@UuidGenerator`를 지정할 때 생성할 UUID의 구체적인 버전을 선택할 수 있다 . 이는 style parameter로 정의된다. 이 매개변수가 취할 수 있는 값이다.

* RANDOM -난수를 기반으로 UUID 생성(RFC 버전 4)

* TIME - 시간 기반 UUID 생성(RFC 버전 1)

* AUTO - 이것은 기본 옵션이며 RANDOM과 동일

Hibernate에서 생성된 UUID 버전 제어 방법이다.

```java
@Entity
class WebSiteUser {

    @Id
    @UuidGenerator(style = UuidGenerator.Style.TIME)
    private UUID id;

    private LocalDate registrationDate;

    // getters and setters
}
```

이제 확인할 수 있듯이 Hibernate는 time-based(version 1) UUID를 생성한다.

```java
@Test
public void whenGeneratingTimeBasedUUID_thenUUIDGeneratedVersion1() throws IOException {
    WebSiteUser user = new WebSiteUser();
    user.setRegistrationDate(LocalDate.now());
    UUID saved = (UUID) session.save(user);
    Assertions.assertThat(saved).isNotNull();
    Assertions.assertThat(saved.version()).isEqualTo(1);
}
```

## 3. UUID로서의 String
Hibernate는 Java ID 유형으로 String을 사용하는 경우 UUID를 생성할 만큼 지능적이다.

```java
@Entity
class Element {

    @Id
    @UuidGenerator
    private String id;

    private String name;
}
```

보시다시피 Hibernate는 String과 UUID Java 유형을 모두 처리할 수 있다.

```java
@Test
public void whenGeneratingUUIDAsString_thenUUIDGeneratedVersion1() throws IOException {
    Element element = new Element();
    element.setName("a");
    String saved = (String) session.save(element);
    Assertions.assertThat(saved).isNotEmpty();
    Assertions.assertThat(UUID.fromString(saved).version()).isEqualTo(4);
}
```

여기서, 열을 `java.util.UUID` 유형으로 설정할 때 Hibernate가 데이터베이스의 해당 UUID 유형에 매핑하려고 시도한다. 이 유형은 데이터베이스마다 다를 수 있다.

따라서 정확한 유형은 실질적으로 Hibernate dialect set에 따라 달라진다. 예를 들어 PostgreSQL을 사용하는 경우 해당 유형은 PostgreSQL의 UUID가 된다. Microsoft SQL Server를 사용하는 경우 해당 유형은 UNIQUEIDENTIFIER가 된다. 그러나 Java ID 유형으로 String을 사용하는 경우 Hibernate는 이를 TEXT 또는 VARCHAR와 같은 일부 SQL 텍스트 유형으로 매핑한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-hibernate-uuid-primary-key](https://www.baeldung.com/java-hibernate-uuid-primary-key)
