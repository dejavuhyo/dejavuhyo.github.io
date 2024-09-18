---
title: JPA 복합 기본 키
author: dejavuhyo
date: 2024-09-19 08:53:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-primary-keys, composite-primary-keys, composite-keys, jpa-기본-키, 복합-키]
---

## 1. 복합 기본 키
복합 기본 키는 복합 키라고도 하며, 두 개 이상의 열을 조합하여 테이블의 기본 키를 형성하는 것이다.

JPA에서는 복합 키를 정의하는데 `@IdClass`와 `@EmbeddedId` 주석이라는 두 가지 옵션이 있다.

복합 기본 키를 정의하려면 몇 가지 규칙을 따라야 한다.

* 복합 기본 키 클래스는 공개되어야 한다.

* 인수가 없는 생성자가 있어야 한다.

* `equals()` 및 `hashCode()` 메서드를 정의해야 한다.

* 직렬화가 가능 해야 한다.

## 2. IdClass 주석
Account라는 테이블이 있고 거기에 accountNumber와 accountType이라는 두 개의 열이 있으며, 이것이 복합 키를 형성한다고 가정한다. 이제 JPA에서 매핑해야 한다.

JPA 사양에 따라 다음과 같은 기본 키 필드를 사용하여 AccountId 클래스를 만든다.

```java
public class AccountId implements Serializable {
    private String accountNumber;

    private String accountType;

    // default constructor

    public AccountId(String accountNumber, String accountType) {
        this.accountNumber = accountNumber;
        this.accountType = accountType;
    }

    // equals() and hashCode()
}
```

다음으로 AccountId 클래스를 엔티티 Account와 연결한다.

그렇게 하려면 `@IdClass` 주석으로 엔티티에 주석을 달아야 한다. 또한 엔티티 Account에서 AccountId 클래스의 필드를 선언 하고 `@Id`로 주석을 달아야 한다.

```java
@Entity
@IdClass(AccountId.class)
public class Account {
    @Id
    private String accountNumber;

    @Id
    private String accountType;

    // other fields, getters and setters
}
```

## 3. EmbeddedId 주석
`@EmbeddedId`는 `@IdClass` 주석의 대안이다.

제목과 언어를 기본 키 필드로 하여 Book에 대한 일부 정보를 유지해야 하는 또 다른 예이다.

이 경우 기본 키 클래스인 BookId에 `@Embeddable` 주석을 추가해야 한다.

```java
@Embeddable
public class BookId implements Serializable {
    private String title;
    private String language;

    // default constructor

    public BookId(String title, String language) {
        this.title = title;
        this.language = language;
    }

    // getters, equals() and hashCode() methods
}
```

그런 다음 `@EmbeddedId`를 사용하여 이 클래스를 Book 엔터티에 포함해야 한다.

```java
@Entity
public class Book {
    @EmbeddedId
    private BookId bookId;

    // constructors, other fields, getters and setters
}
```

## 4. @IdClass vs @EmbeddedId
둘 사이의 표면적 차이점은 `@IdClass`를 사용하면 열을 두 번 지정해야 한다는 것이다. 한 번은 AccountId에서, 또 한 번은 Account에서 지정해야 한다. 하지만 `@EmbeddedId`를 사용하면 그럴 필요가 없다.

하지만 그 외에도 몇 가지 단점도 있다.

예를 들어, 이러한 다양한 구조는 우리가 작성하는 JPQL 쿼리에 영향을 미친다.

`@IdClass`를 사용하면 쿼리가 좀 더 간단해진다.

```sql
SELECT account.accountNumber FROM Account account
```

`@EmbeddedId`를 사용하면 한 번의 추가 탐색을 수행해야 한다.

```sql
SELECT book.bookId.title FROM Book book
```

또한, `@IdClass`는 수정할 수 없는 복합 키 클래스를 사용하는 곳에서 매우 유용할 수 있다.

복합 키의 일부에 개별적으로 액세스하려는 경우 `@IdClass`를 사용할 수 있지만 전체 식별자를 객체로 자주 사용하는 경우 `@EmbeddedId`를 사용하는 것이 좋다.

## 5. 복합 기본 키를 사용한 카운트 쿼리
[JPQL](https://www.baeldung.com/spring-data-jpa-query)과 [CriteriaQuery](https://www.baeldung.com/hibernate-criteria-queries)를 사용하면 내장된 ID를 기본 키로 사용하여 엔터티를 계산할 수 있다.

### 1) JPQL 사용
엔티티 관리자를 사용하여 엔티티에 대한 카운트를 수행하고 이를 실행하기 위해 JPQL 쿼리를 작성할 수 있다. `getSingleResult()` 메서드는 책 엔티티에 대한 카운트를 반환한다.

```java
long countBookByEmbeddedIdUsingJPQL() {
    String jpql = "SELECT count(b) FROM Book b";
    Query query = em.createQuery(jpql);
    return (long) query.getSingleResult();
}
```

단위 테스트를 통해 결과를 확인할 수 있다.

```java
@Test
public void givenBookWithEmbeddedId_whenCountCalled_thenReturnsCount() {
    IntStream.rangeClosed(1, 10)
      .forEach(i -> {
          BookId bookId = new BookId("Book" + i, "English");
          Book book = new Book(bookId);
          book.setDescription("Novel and Historical Fiction" + i);
          persist(book);
      });
    assertEquals(10, countBookByEmbeddedIdUsingJPQL());
    assertEquals(10, countBookByEmbeddedIdUsingCriteriaQuery());
}
```

### 2) CriteriaQuery 사용
CriteriaQuery를 사용하여 임베디드 ID를 사용하여 엔터티를 계산할 수 있다. 먼저 루트 쿼리를 빌드한 다음 `getSingleResult()`를 사용하여 루트 쿼리에 대한 계산을 반환한다.

```java
long countBookByEmbeddedIdUsingCriteriaQuery() {
    CriteriaBuilder criteriaBuilder = em.getCriteriaBuilder();
    CriteriaQuery<Long> criteriaQuery = criteriaBuilder.createQuery(Long.class);
    Root<Book> root = criteriaQuery.from(Book.class);
    criteriaQuery.select(criteriaBuilder.count(root));

    return em.createQuery(criteriaQuery).getSingleResult();
}
```

단위 테스트를 통해 결과를 확인할 수 있다.

```java
@Test
public void givenBookWithEmbeddedId_whenCountCalled_thenReturnsCount() {
    IntStream.rangeClosed(1, 10)
      .forEach(i -> {
          BookId bookId = new BookId("Book" + i, "English");
          Book book = new Book(bookId);
          book.setDescription("Novel and Historical Fiction" + i);
          persist(book);
      });
    assertEquals(10, countBookByEmbeddedIdUsingJPQL());
    assertEquals(10, countBookByEmbeddedIdUsingCriteriaQuery());
}
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-composite-primary-keys](https://www.baeldung.com/jpa-composite-primary-keys)
