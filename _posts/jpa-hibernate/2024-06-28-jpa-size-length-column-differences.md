---
title: JPA @Size, @Length, @Column(length=value) 차이점
author: dejavuhyo
date: 2024-06-28 14:56:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa, jpa-size, jpa-length, jpa-column, size-length-column]
---

## 1. 유래
이러한 주석은 모두 필드 크기를 전달하기 위한 것이다.

`@Size`와 `@Length`는 유사하다. 두 주석 중 하나를 사용하여 필드 크기의 유효성을 검사할 수 있다. 전자는 Java 표준 주석인 반면 후자는 Hibernate에만 해당 된다.

`@Column`은 DDL 문을 제어하는데 사용하는 JPA 주석 이다.

## 2. @Size
검증을 위해 `@Size`, 빈 검증 주석을 사용한다. `@Size`로 주석 처리된 속성 middleName을 사용하여 속성 min과 max 사이의 값을 검증한다.

```java
public class User {

    // ...

    @Size(min = 3, max = 15)
    private String middleName;

    // ...

}
```

가장 중요한 점은 `@Size`가 빈을 JPA와 Hibernate와 같은 벤더에 독립적으로 만든다는 것이다. 결과적으로 `@Length` 보다 더 이식성이 뛰어나다.

## 3. @Length
`@Length`는 `@Size`의 Hibernate 전용 버전이다. `@Length`를 사용하여 lastName의 범위를 적용한다.

```java
@Entity
public class User {

    // ...
      
    @Length(min = 3, max = 15)
    private String lastName;

    // ...

}
```

## 4. @Column(length=value)
`@Column`은 이전 두 주석과 상당히 다르다.

`@Column`을 사용하여 물리적 데이터베이스 열의 구체적인 특성을 나타낸다. `@Column` 주석의 length 속성을 사용하여 문자열 값 열 길이를 지정한다.

```java
@Entity
public class User {

    @Column(length = 3)
    private String firstName;

    // ...

}
```

결과 열은 `VARCHAR(3)`으로 생성되고 더 긴 문자열을 삽입하려고 하면 SQL 오류가 발생한다.

`@Column`은 유효성 검사를 제공하지 않으므로 테이블 열 속성을 지정하는 데만 사용한다.

물론 `@Column`을 `@Size`와 함께 사용하여 Bean 유효성 검사와 함께 데이터베이스 열 속성을 지정할 수 있다.

```java
@Entity
public class User {

    // ... 
    
    @Column(length = 5)
    @Size(min = 3, max = 5)
    private String city;

    // ...

}
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-size-length-column-differences](https://www.baeldung.com/jpa-size-length-column-differences)
