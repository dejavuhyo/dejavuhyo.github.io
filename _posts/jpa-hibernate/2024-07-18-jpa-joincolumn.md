---
title: JPA @JoinColumn Annotation
author: dejavuhyo
date: 2024-07-18 16:32:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-joincolumn, joincolumn, joincolumn-annotation]
---

## 1. @OneToOne 매핑 예제
`@JoinColumn` 주석과 `@OneToOne` 매핑을 결합하면 소유자 엔터티의 주어진 열이 참조 엔터티의 기본 키를 참조한다는 것을 나타낸다.

```java
@Entity
public class Office {
    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "addressId")
    private Address address;
}
```

위의 코드 예제는 Office 엔터티를 Address 엔터티의 기본 키와 연결하는 외래 키를 생성한다. Office 엔터티의 외래 키 열 이름은 name 속성으로 지정된다.

## 2. @OneToMany 매핑 예제
`@OneToMany` 매핑을 사용하는 경우 mappedBy 매개변수를 사용하여 지정된 열이 다른 엔터티의 소유임을 나타낼 수 있다.

```java
@Entity
public class Employee {
 
    @Id
    private Long id;

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "employee")
    private List<Email> emails;
}

@Entity
public class Email {
 
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "employee_id")
    private Employee employee;
}
```

위의 예에서 Email(소유자 엔터티)에는 ID 값을 저장하는 조인 열 employee_id가 있으며 Employee 엔터티에 대한 외래 키가 있다.

![joincol-1](/assets/img/2024-07-18-jpa-joincolumn/joincol-1.png)

## 3. @JoinColumns
여러 개의 조인 열을 생성하려는 상황에서는 `@JoinColumns` 주석을 사용할 수 있다.

```java
@Entity
public class Office {
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumns({
        @JoinColumn(name="ADDR_ID", referencedColumnName="ID"),
        @JoinColumn(name="ADDR_ZIP", referencedColumnName="ZIP")
    })
    private Address address;
}
```

위의 예제에서는 Address 엔터티의 ID 및 ZIP 열을 가리키는 두 개의 외래 키를 생성한다.

![joincol-2](/assets/img/2024-07-18-jpa-joincolumn/joincol-2.png)

## [출처 및 참고]
* [https://www.baeldung.com/jpa-join-column](https://www.baeldung.com/jpa-join-column)
