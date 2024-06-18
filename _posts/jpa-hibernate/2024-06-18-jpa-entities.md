---
title: JPA Entities
author: dejavuhyo
date: 2024-06-18 16:36:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa, jpa-entities, jpa-엔터티]
---

## 1. Entity
JPA의 엔터티는 데이터베이스에 지속될 수 있는 데이터를 나타내는 POJO일 뿐이다. 엔터티는 데이터베이스에 저장된 테이블을 나타낸다. 엔터티의 모든 인스턴스는 테이블의 행을 나타낸다.

### 1) Entity Annotation
학생의 데이터를 나타내는 Student라는 POJO가 있고 이를 데이터베이스에 저장하고 싶다고 가정한다.

```java
public class Student {
    
    // fields, getters and setters
    
}
```

이를 위해서는 JPA가 이를 인식할 수 있도록 엔터티를 정의해야 한다.

`@Entity` 주석을 사용하여 정의한다. 이 주석을 클래스 수준에서 지정해야 한다. 또한 엔터티에 인수가 없는 생성자와 기본 키가 있는지 확인해야 한다.

```java
@Entity
public class Student {
    
    // fields, getters and setters
    
}
```

엔터티 이름은 기본적으로 클래스 이름으로 설정된다. name 요소를 사용하여 name을 변경할 수 있다.

```java
@Entity(name="student")
public class Student {
    
    // fields, getters and setters
    
}
```

다양한 JPA 구현은 해당 기능을 제공하기 위해 엔터티를 서브클래싱하려고 시도하므로 엔터티 클래스를 final로 선언하면 안된다.

### 2) Id Annotation
각 JPA 엔터티에는 이를 고유하게 식별하는 기본 키가 있어야 한다. `@Id` 주석은 기본 키를 정의한다. `@GeneratedValue` 주석에 지정된 다양한 방법으로 식별자를 생성할 수 있다.

strategies 요소를 사용하여 네 가지 ID 생성 전략 중에서 선택할 수 있다. 값은 `AUTO`, `TABLE`, `SEQUENCE` 또는 `IDENTITY`이다.

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    
    private String name;
    
    // getters and setters
}
```

`GenerationType.AUTO`를 지정하는 경우, JPA 제공자는 식별자를 생성하기 위해 원하는 모든 전략을 사용한다.

엔터티 필드에 주석을 추가하면 JPA 공급자는 이러한 필드를 사용하여 엔터티 상태를 가져오고 설정한다. 필드 액세스 외에도 속성 액세스 또는 혼합 액세스를 수행할 수도 있다. 이를 통해 동일한 엔터티에서 필드 및 속성 액세스를 모두 사용할 수 있다.

### 3) Table Annotation
대부분의 경우 데이터베이스의 테이블 이름과 엔터티 이름은 동일하지 않다.

이러한 경우 `@Table` 주석을 사용하여 테이블 이름을 지정할 수 있다.

```java
@Entity
@Table(name="STUDENT")
public class Student {
    
    // fields, getters and setters
    
}
```

스키마 요소를 사용하여 스키마를 언급할 수도 있다.

```java
@Entity
@Table(name="STUDENT", schema="SCHOOL")
public class Student {
    
    // fields, getters and setters
    
}
```

스키마 이름은 한 테이블 세트를 다른 테이블 세트와 구별하는데 도움이 된다.

`@Table` 주석을 사용하지 않으면 테이블 이름이 엔터티 이름이 된다.

### 4) Column Annotation
`@Table` 주석과 마찬가지로 `@Column` 주석을 사용하여 테이블의 열 세부정보를 언급할 수 있다.

`@Column` 주석에는 `name`, `length`, `nullable` 및 `unique` 같은 많은 요소가 있다.

```java
@Entity
@Table(name="STUDENT")
public class Student {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    
    @Column(name="STUDENT_NAME", length=50, nullable=false, unique=false)
    private String name;
    
    // other fields, getters and setters
}
```

`name` 요소는 테이블의 열 이름을 지정한다. `length` 요소는 길이를 지정한다. `nullable` 요소는 열이 null을 허용하는지 여부를 지정하고, `unique` 요소는 열이 고유한지 여부를 지정한다.

이 주석을 지정하지 않으면 테이블의 열 이름이 필드 이름이 된다.

### 5) Transient Annotation
때로는 필드를 비영구적으로 만들고 싶을 수도 있다. 이를 위해 `@Transient` 주석을 사용할 수 있다. 필드가 유지되지 않도록 지정한다.

예를 들어, 생년월일을 기준으로 학생의 나이를 계산할 수 있다.

`@Transient` 주석을 사용하여 필드 연령에 주석을 달아본다.

```java
@Entity
@Table(name="STUDENT")
public class Student {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    
    @Column(name="STUDENT_NAME", length=50, nullable=false)
    private String name;
    
    @Transient
    private Integer age;
    
    // other fields, getters and setters
}
```

결과적으로 필드 연령은 테이블에 유지되지 않는다.

### 6) Temporal Annotation
어떤 경우에는 테이블에 임시 값을 저장해야 할 수도 있다.

이를 위해 `@Temporal` 주석이 있다.

```java
@Entity
@Table(name="STUDENT")
public class Student {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    
    @Column(name="STUDENT_NAME", length=50, nullable=false, unique=false)
    private String name;
    
    @Transient
    private Integer age;
    
    @Temporal(TemporalType.DATE)
    private Date birthDate;
    
    // other fields, getters and setters
}
```

그러나 JPA 3.1에서는 `java.time.LocalDate`, `java.time.LocalTime`, `java.time.LocalDateTime`, `java.time.OffsetTime` 및 `java.time.OffsetDateTime`도 지원한다.

### 7) Enumerated Annotation
때로는 Java 열거형 유형을 유지하고 싶을 수도 있다.

`@Enumerated` 주석을 사용하여 열거형이 이름으로 유지되어야 하는지 아니면 서수(기본값)로 유지되어야 하는지 지정할 수 있다.

```java
public enum Gender {
    MALE, 
    FEMALE
}
```

```java
@Entity
@Table(name="STUDENT")
public class Student {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    
    @Column(name="STUDENT_NAME", length=50, nullable=false, unique=false)
    private String name;
    
    @Transient
    private Integer age;
    
    @Temporal(TemporalType.DATE)
    private Date birthDate;
    
    @Enumerated(EnumType.STRING)
    private Gender gender;
    
    // other fields, getters and setters
}
```

enum의 서수로 Gender를 유지하려는 경우 `@Enumerated` 주석을 전혀 지정할 필요가 없다.

그러나 열거형 이름으로 성별을 유지하기 위해 `EnumType.STRING`을 사용하여 주석을 구성했다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-entities](https://www.baeldung.com/jpa-entities)
