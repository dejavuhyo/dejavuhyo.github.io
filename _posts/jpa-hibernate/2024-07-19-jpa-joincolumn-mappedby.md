---
title: JPA @JoinColumn과 mappedBy 차이점
author: dejavuhyo
date: 2024-07-19 11:12:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-joincolumn-mappedby, joincolumn-mappedby, jpa-joincolumn, jpa-mappedby]
---

## 1. 차이점
JPA 관계는 단방향 또는 양방향일 수 있다. 이는 단순히 연관된 엔터티 중 하나 또는 둘 다에 대한 속성으로 모델링할 수 있음을 의미한다.

엔티티 간 관계의 방향을 정의하는 것은 데이터베이스 매핑에 영향을 미치지 않는다. 도메인 모델에서 해당 관계를 사용하는 방향만 정의한다.

양방향 관계의 경우 일반적으로 다음을 정의한다.

* 소유 측

* 역 또는 참조하는 측면

`@JoinColumn` 주석은 엔티티 연결이나 요소 컬렉션을 조인하는데 사용할 열을 지정하는데 도움이 된다. 반면 `mappedBy` 속성은 관계의 참조 측(비소유 측)을 정의하는데 사용된다.

## 2. 초기 설정
Employee와 Email이라는 두 개의 엔터티가 있다고 가정한다.

분명히, 직원은 여러 개의 이메일 주소를 가질 수 있다. 그러나 주어진 이메일 주소는 정확히 한 직원에게 속할 수 있다.

이는 이들이 일대다 연결을 공유한다는 것을 의미한다.

![initial-setup](/assets/img/2024-07-19-jpa-joincolumn-mappedby/initial-setup.png)

또한 RDBMS 모델에서는 Email 엔터티에 Employee의 id 속성을 참조하는 외래 키 employee_id가 있다.

## 3. @JoinColumn 주석
일대다/다대일 관계에서 소유 측은 일반적으로 관계의 많은 측에서 정의된다. 일반적으로 외래 키를 소유하는 측이다.

`@JoinColumn` 주석은 소유 측의 실제 물리적 매핑을 정의한다.

```java
@Entity
public class Email {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "employee_id")
    private Employee employee;

    // ...

}
```

이는 간단히 말해 Email 엔터티에 Employee 엔터티의 기본 속성 id를 참조하는 employee_id라는 외래 키 열이 있다는 것을 의미한다.

## 4. mappedBy 속성
관계의 소유 측면을 정의하고 나면 Hibernate는 이미 데이터베이스에 해당 관계를 매핑하는데 필요한 모든 정보를 갖고 있다.

이 연결을 양방향으로 만들려면 참조하는 쪽을 정의하기만 하면 된다. 반대 또는 참조하는 쪽은 단순히 소유하는 쪽에 매핑된다.

`@OneToMany` 주석의 mappedBy 속성을 사용하면 쉽게 그렇게 할 수 있다.

Employee 엔터티를 정의한다.

```java
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "employee")
    private List<Email> emails;
    
    // ...
}
```

mappedBy의 값은 소유 측의 연관 매핑 속성의 이름이다. 이를 통해 이제 Employee와 Email 엔터티 간에 양방향 연관이 설정되었다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-joincolumn-vs-mappedby](https://www.baeldung.com/jpa-joincolumn-vs-mappedby)
