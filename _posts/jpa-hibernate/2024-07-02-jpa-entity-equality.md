---
title: JPA Entity Equality
author: dejavuhyo
date: 2024-07-02 11:24:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-entity, entity-equality, 엔티티-동등성]
---

## 1. 고려사항
일반적으로 동등성은 단순히 두 객체가 동일하다는 것을 의미한다. 그러나 Java에서는 `Object.equals()` 및 `Object.hashCode()` 메서드를 재정의하여 동등성의 정의를 변경할 수 있다. 궁극적으로 Java는 동등함이 무엇을 의미하는지 정의할 수 있도록 한다. 그러나 먼저 고려해야 할 몇 가지 사항이 있다.

### 1) Collections
Java 컬렉션은 객체를 그룹화한다. 그룹화 논리는 해시 코드라고 알려진 특수 값을 사용하여 객체의 그룹을 결정한다.

`hashCode()` 메서드에서 반환된 값이 모든 엔터티에 대해 동일하면 원치 않는 동작이 발생할 수 있다. 엔터티 객체에 id로 정의된 기본 키가 있지만 `hashCode()` 메서드를 다음과 같이 정의한다고 가정한다.

```java
@Override
public int hashCode() {
    return 12345;
}
```

컬렉션은 서로 다른 객체를 비교할 때 구별할 수 없다. 왜냐하면 모두 같은 해시 코드를 공유하기 때문이다. 다행히도, 해시 코드를 생성할 때 고유 키를 사용하는 것만큼 쉽게 이를 해결할 수 있다. 예를 들어, id를 사용하여 `hashCode()` 메서드를 정의할 수 있다.

```java
@Override
public int hashCode() {
    return id * 12345;
}
```

이 경우, 엔티티의 id를 사용하여 해시 코드를 정의했다. 이제 컬렉션은 엔티티를 비교, 정렬, 저장할 수 있다.

### 2) 일시적 Entities
지속성 컨텍스트와 연관이 없는 새로 생성된 JPA 엔티티 객체는 일시적 상태에 있는 것으로 간주된다. 이러한 객체는 일반적으로 `@Id` 멤버가 채워지지 않는다. 따라서 `equals()` 또는 `hashCode()`가 계산에 id를 사용하는 경우 모든 일시적 객체는 모두 id 가 null이 되기 때문에 동일해진다. 이것이 바람직한 경우는 많지 않다.

### 3) Subclasses
동등성을 정의할 때 하위 클래스도 문제가 된다. `equals()` 메서드에서 클래스를 비교하는 것이 일반적이다. 따라서 `getClass()` 메서드를 포함하면 객체를 동등성 비교 시 하위 클래스를 걸러내는데 도움이 된다.

객체가 같은 클래스이고 같은 id를 가지고 있는 경우에만 작동하는 `equals()` 메서드를 정의한다.

```java
@Override
public boolean equals(Object o) {
    if (o == null || this.getClass() != o.getClass()) {
        return false;
    }
    return o.id.equals(this.id);
}
```

## 2. Equality 정의
이러한 고려 사항을 감안할 때, equality를 처리할 때 몇 가지 선택권을 가지고 있다. 따라서 접근 방식은 객체를 어떻게 사용할 계획인지에 대한 세부 사항에 따라 달라진다.

### 1) 오버라이드 없음
기본적으로 Java는 Object 클래스에서 파생된 모든 객체 덕분에 `equals()` 및 `hashCode()` 메서드를 제공한다. 불행히도 이는 객체를 비교할 때 동일하다고 간주되려면 동일한 인스턴스여야 하며 동일한 객체를 나타내는 두 개의 별도 인스턴스가 아니어야 함을 의미한다.

### 2) 데이터베이스 키 사용
대부분의 경우, 데이터베이스에 저장된 JPA 엔티티를 다루고 있다. 일반적으로 이러한 엔티티는 고유한 값인 기본 키를 갖는다. 따라서 동일한 기본 키 값을 갖는 이 엔티티의 모든 인스턴스는 동일하다. 따라서 하위 클래스에 대해 `equals()`를 재정의 하고 두 경우 모두 기본 키만 사용하여 `hashCode()`를 재정의 할 수 있다.

### 3) 비즈니스 키 사용
비즈니스 키를 사용하여 JPA 엔터티를 비교할 수 있다. 이 경우 객체의 키는 기본 키 이외의 엔터티 멤버로 구성된다. 이 키는 JPA 엔터티를 고유하게 만들어야 한다. 비즈니스 키를 사용하면 기본 키나 데이터베이스에서 생성된 키가 필요 없이 엔터티를 비교할 때 동일한 원하는 결과를 얻을 수 있다.

이메일 주소는 `@Id` 필드가 아니더라도 항상 고유할 것이라는 것을 알고 있다고 가정한다. `hashCode()` 및 `equals()` 메서드에 이메일 필드를 포함할 수 있다.

```java
public class EqualByBusinessKey {

    private String email;

    @Override
    public int hashCode() {
        return java.util.Objects.hashCode(email);
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj == null) {
            return false;
        }
        if (obj instanceof EqualByBusinessKey) {
            if (((EqualByBusinessKey) obj).getEmail().equals(getEmail())) {
                return true;
            }
        }

        return false;
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-entity-equality](https://www.baeldung.com/jpa-entity-equality)
