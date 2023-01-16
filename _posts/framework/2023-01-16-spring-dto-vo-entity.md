---
title: Spring DTO vs VO vs Entity
author: dejavuhyo
date: 2023-01-16 20:45:00 +0900
categories: [Application, Framework]
tags: [spring-dto, spring-vo, spring-entity, dto, vo, entity, dto-vo-entity, dto-vo-entity-비교]
---

## 1. DTO
DTO(Data Transfer Object)는 데이터 전송(이동) 객체라는 의미가 있다.

데이터 교환만을 위해 사용하기 때문에 로직이 없고 getter, setter 메소드로만 구성되는 POJO이다.

* DTO 예제

```java
@Getter 
@Setter
class ArticleDTO {
    private String title;
    private String content;
    private String writer;
}

```

## 2. VO
VO(Value Object)는 값 객체라는 의미가 있다. VO의 핵심 역할은 `equals()`와 `hashcode()`를 오버 라이딩하는 것이다.

로직을 포함할 수 있으며, 객체의 불변성(객체의 정보가 변경하지 않음)을 보장한다.

VO 내부에 선언된 속성(Field)의 모든 값이 VO 객체마다 값이 같아야 똑같은 객체라고 판별한다.

* VO 예제

```java
@Getter
@Setter
@Alias("article")
class ArticleVO {
    private Long id;
    private String title;
    private String contents;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Article article = (Article) o;
        return Objects.equals(id, article.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

## 3. Entity
Entity Class는 실제 DataBase의 테이블과 1:1로 Mapping 되는 Class로, DB의 테이블 내에 존재하는 컬럼만을 속성(필드)으로 가져야 한다.

Entity Class는 상속받거나 구현체여서는 안 되며, 테이블 내에 존재하지 않는 컬럼을 가져서도 안 된다.

* Entity 예제

```java
@Getter
@ToString
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@EqualsAndHashCode(of = "id")
@Table(name = "user")
@Entity
public class User {

    @Id
    @GeneratedValue
    private int id;

    @Column(name = "name", nullable = false)
    private String name;

    @Column(name = "password", nullable = false)
    private String password;

    @Column(name = "email", nullable = false, unique = true)
    private String email;

    @Column(name = "phone", nullable = false, unique = true)
    private String phone;

    @Column(nullable = true)
    private LocalDateTime create_date;

    private LocalDateTime modify_date;

    @Builder(
            builderClassName = "init"
            , builderMethodName = "initUser"
    )
    private User(UserDTO dto) {
        id = dto.getId();
        name = dto.getName();
        ...
    }
}
```

## 4. VO와 DTO 차이점
VO는 DTO와 Data를 전달하는 객체로 동일한 개념이지만, VO는 조금 더 특정한 Business Logic의 결괏값을 담는 객체이다.

equals, hashCode Method를 구현하여 특정 중요한 Data를 전달할 때는 VO를 생성하여 이를 동일한 객체 비교까지 필요한 Logic 내에서 주로 사용하게 된다.

DTO는 Layer 간의 단순 통신 용도로 오가는 Data 전달 객체이다. 조금 더 포괄적으로 제한 없이 사용할 수 있는 객체이다.

민감하지 않거나 해당 객체 안의 값들을 통해 동일한 객체임을 비교하는 로직에 사용되지 않을 때는 단순하게 DTO를 사용하면 된다.

![dto-vo-entity](/assets/img/2023-01-16-spring-dto-vo-entity/dto-vo-entity.png)

## [출처 및 참고]
* [https://youngjinmo.github.io/2021/04/dto-vo-entity/](https://youngjinmo.github.io/2021/04/dto-vo-entity/)
* [https://velog.io/@gillog/Entity-DTO-VO-%EB%B0%94%EB%A1%9C-%EC%95%8C%EA%B8%B0](https://velog.io/@gillog/Entity-DTO-VO-%EB%B0%94%EB%A1%9C-%EC%95%8C%EA%B8%B0)
