---
title: JPA 다대다 관계
author: dejavuhyo
date: 2024-07-17 14:05:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-many-to-many, jpa-다대다, 다대다-관계]
---

## 1. 기본 다대다

### 1) 다대다 관계 모델링
관계는 두 유형의 엔터티 간의 연결이다. 다대다 관계의 경우, 양측은 다른 측의 여러 인스턴스와 관련될 수 있다.

엔티티 유형이 서로 관계를 맺을 수 있다. 가계도 모델링의 예를 생각해 본다. 모든 노드는 사람이기 때문에 부모-자식 관계에 대해 이야기하면 두 참여자 모두 사람이 된다.

하지만, 단일 또는 다중 엔터티 유형 간의 관계에 대해 이야기하든 그렇게 큰 차이는 없다. 두 개의 다른 엔터티 유형 간의 관계에 대해 생각하는 것이 더 쉽기 때문에, 그것을 사용하여 사례를 설명한다.

학생들이 자신이 좋아하는 과목을 표시하는 예를 들어본다.

학생은 여러 과목을 좋아할 수 있으며, 많은 학생이 같은 과목을 좋아할 수도 있다.

![simple-er](/assets/img/2024-01-01-jpa-many-to-many/simple-er.png)

알다시피, RDBMS에서는 외래 키로 관계를 만들 수 있다. 양쪽 모두 다른 쪽을 참조할 수 있어야 하므로 외래 키를 보관할 별도의 테이블을 만들어야 한다.

![simple-model-updated](/assets/img/2024-01-01-jpa-many-to-many/simple-model-updated.png)

이러한 테이블을 조인 테이블이라고 한다. 조인 테이블에서 외래 키의 조합은 복합 기본 키가 된다.

### 2) JPA 구현
POJO로 다대다 관계를 모델링하는 것은 쉽다. 두 클래스 모두에 다른 요소를 포함하는 Collection을 포함 해야 한다.

그런 다음 클래스를 `@Entity`로 표시 하고 기본 키를 `@Id`로 표시하여 적절한 JPA 엔터티로 만들어야 한다.

또한, 관계 유형을 구성해야 한다. 따라서 컬렉션에 `@ManyToMany` 주석을 표시한다.

```java
@Entity
class Student {

    @Id
    Long id;

    @ManyToMany
    Set<Course> likedCourses;

    // additional properties
    // standard constructors, getters, and setters
}

@Entity
class Course {

    @Id
    Long id;

    @ManyToMany
    Set<Student> likes;

    // additional properties
    // standard constructors, getters, and setters
}
```

또한, RDBMS에서 관계를 모델링하는 방법을 구성해야 한다.

소유자 측은 관계를 구성하는 곳이다. Student 클래스를 사용한다.

Student 클래스의 `@JoinTable` 주석으로 이를 수행할 수 있다. `@JoinColumn` 주석으로 조인 테이블 이름(course_like)과 외래 키를 제공한다. joinColumn 속성은 관계의 소유자 측에 연결되고 inverseJoinColumn은 다른 측에 연결된다.

```java
@ManyToMany
@JoinTable(
  name = "course_like", 
  joinColumns = @JoinColumn(name = "student_id"), 
  inverseJoinColumns = @JoinColumn(name = "course_id"))
Set<Course> likedCourses;
```

`@JoinTable` 또는 `@JoinColumn`을 사용하는 것은 필수가 아니다. JPA는 테이블과 열 이름을 생성한다. 그러나 JPA가 사용하는 전략은 항상 사용하는 명명 규칙과 일치하지 않다. 따라서 테이블과 열 이름을 구성할 수 있는 가능성이 필요하다.

대상 측에서는 관계를 매핑하는 필드의 이름만 제공하면 된다.

따라서 Course 클래스의 `@ManyToMany` 주석의 mappedBy 속성을 설정한다.

```java
@ManyToMany(mappedBy = "likedCourses")
Set<Student> likes;
```

다 대다 관계에는 데이터베이스의 소유자 측이 없으므로 Course 클래스에서 조인 테이블을 구성하고 Student 클래스에서 참조할 수 있다.

## 2. 복합 키 사용 다대다

### 1) 관계 속성 모델링
학생들이 과목을 평가하게 하고 싶다고 가정한다. 학생은 많은 과목을 평가할 수 있고, 많은 학생이 같은 과목을 평가할 수 있다. 따라서 이는 다대다 관계이기도 한다.

이 예를 조금 더 복잡하게 만드는 것은 평가 관계에는 그것이 존재한다는 사실 이상의 것이 있다는 것이다. 학생이 과정에서 준 평가 점수를 저장해야 한다.

이 정보의 저장은 학생이 다른 과목에 다른 평가를 줄 수 있으므로 Student 엔터티에 넣을 수 없다. 마찬가지로 Course 엔터티에 저장하는 것도 좋은 해결책이 아니다.

이는 관계 자체에 속성이 있는 상황이다.

이 예를 사용하면 ER 다이어그램에서 관계에 속성을 첨부하는 것은 다음과 같다.

![relation-attribute-er](/assets/img/2024-01-01-jpa-many-to-many/relation-attribute-er.png)

간단한 다대다 관계와 거의 같은 방식으로 모델링할 수 있다. 유일한 차이점은 조인 테이블에 새 속성을 첨부한다는 것이다.

![relation-attribute-model-updated](/assets/img/2024-01-01-jpa-many-to-many/relation-attribute-model-updated.png)

### 2) JPA 복합 키 생성
간단한 다대다 관계의 구현은 다소 간단했다. 유일한 문제는 엔티티를 직접 연결했기 때문에 그런 방식으로 관계에 속성을 추가할 수 없다는 것이다. 따라서 관계 자체에 속성을 추가할 방법이 없다.

JPA에서 DB 속성을 클래스 필드에 매핑하므로 관계에 대한 새로운 엔터티 클래스를 만들어야 한다.

물론, 모든 JPA 엔티티에는 기본 키가 필요하다. 기본 키는 복합 키이므로 키의 다른 부분을 보관할 새 클래스를 만들어야 한다.

```java
@Embeddable
class CourseRatingKey implements Serializable {

    @Column(name = "student_id")
    Long studentId;

    @Column(name = "course_id")
    Long courseId;

    // standard constructors, getters, and setters
    // hashcode and equals implementation
}
```

복합 키 클래스는 다음과 같은 핵심 요구 사항을 충족해야 한다.

* `@Embeddable`로 표시해야 한다.

* `java.io.Serializable`을 구현해야 한다.

* `hashcode()`와 `equals()` 메서드의 구현을 제공해야 한다.

### 3) JPA 복합 키 사용
이 복합 키 클래스를 사용하면 조인 테이블을 모델링하는 엔티티 클래스를 생성할 수 있다.

```java
@Entity
class CourseRating {

    @EmbeddedId
    CourseRatingKey id;

    @ManyToOne
    @MapsId("studentId")
    @JoinColumn(name = "student_id")
    Student student;

    @ManyToOne
    @MapsId("courseId")
    @JoinColumn(name = "course_id")
    Course course;

    int rating;
    
    // standard constructors, getters, and setters
}
```

이 코드는 일반적인 엔티티 구현과 매우 유사하다. 그러나 몇 가지 주요 차이점이 있다.

* `@EmbeddedId`를 사용하여 기본 키를 표시했는데, 이는 CourseRatingKey 클래스의 인스턴스이다.

* 학생 및 과정 필드를 `@MapsId`로 표시했다.

`@MapsId`는 해당 필드를 키의 일부에 연결하고, 이것이 다대일 관계의 외래 키라는 것을 의미한다.

그런 다음 이전과 마찬가지로 Student 및 Course 엔터티에서 역참조를 구성할 수 있다.

```java
class Student {

    // ...

    @OneToMany(mappedBy = "student")
    Set<CourseRating> ratings;

    // ...
}

class Course {

    // ...

    @OneToMany(mappedBy = "course")
    Set<CourseRating> ratings;

    // ...
}
```

복합 키를 사용하는 또 다른 방법이 있다. 바로 `@IdClass` 주석이다.

### 4) 추가 특성
Student 및 Course 클래스에 대한 관계를 `@ManyToOne`으로 구성했다. 새 엔터티를 사용하여 다대다 관계를 구조적으로 두 개의 다대일 관계로 분해했기 때문에 이렇게 할 수 있었다.

테이블을 자세히 살펴보면, 두 개의 다대일 관계가 포함되어 있음을 알 수 있다. 즉, RDBMS에는 다대다 관계가 없다. 조인 테이블로 만든 구조를 다대다 관계라고 부르는 이유는 우리가 모델링하는 것이 바로 그것이기 때문이다.

게다가, 다대다 관계에 대해 이야기하면 더 명확해진다. 그러는 동안, 조인 테이블은 단지 구현 세부 사항일 뿐이고, 그것에 대해 신경 쓰지 않는다.

언급하지 않은 추가 기능이 있다. 간단한 다대다 솔루션은 두 엔터티 간의 관계를 만든다. 따라서 관계를 더 많은 엔터티로 확장할 수 없다. 하지만 이 솔루션에는 이러한 제한이 없다. 여러 엔터티 유형 간의 관계를 모델링할 수 있다.

예를 들어, 여러 교사가 한 과목을 가르칠 수 있는 경우, 학생들은 특정 교사가 특정 과목을 어떻게 가르치는지 평가할 수 있다. 이런 식으로 평가는 학생, 과목, 교사라는 세 가지 개체 간의 관계가 된다.

## 3. 새로운 엔터티 사용 다대다

### 1) 관계 속성 모델링
학생들이 과목에 등록할 수 있도록 하고 싶다고 가정한다. 또한 학생이 특정 과목에 등록한 시점을 저장해야 한다. 과목에서 받은 성적을 저장하고 싶다.

이상적으로는 이전 솔루션으로 이 문제를 해결할 수 있는데, 여기서 복합 키를 가진 엔터티를 가지고 있었다. 그러나 세상은 이상과는 거리가 멀고, 학생들은 항상 첫 번째 시도에서 과정을 달성하지는 못한다.

이 경우, 같은 student-course 쌍 사이에 여러 연결이 있거나, 같은 student_id-course_id 쌍을 가진 여러 행이 있다. 모든 기본 키가 고유해야 하기 때문에 이전 솔루션을 사용하여 모델링할 수 없다. 따라서 별도의 기본 키를 사용해야 한다.

따라서 등록의 속성을 보유하는 엔터티를 도입할 수 있다.

![relation-entity-er-updated](/assets/img/2024-01-01-jpa-many-to-many/relation-entity-er-updated.png)

이 경우 등록 엔터티는 다른 두 엔터티 간의 관계를 나타낸다.

엔터티이므로 자체 기본 키가 있다.

이전 솔루션에서는 두 개의 외래 키로 복합 기본 키를 생성했다.

이제 두 개의 외래 키는 기본 키의 일부가 되지 않는다.

![relation-entity-model-updated](/assets/img/2024-01-01-jpa-many-to-many/relation-entity-model-updated.png)

### 2) JPA 구현
course_registration이 일반 테이블이 되었으므로 이를 모델링하는 일반적인 JPA 엔터티를 만들 수 있다.

```java
@Entity
class CourseRegistration {

    @Id
    Long id;

    @ManyToOne
    @JoinColumn(name = "student_id")
    Student student;

    @ManyToOne
    @JoinColumn(name = "course_id")
    Course course;

    LocalDateTime registeredAt;

    int grade;
    
    // additional properties
    // standard constructors, getters, and setters
}
```

또한 Student 및 Course 클래스의 관계를 구성해야 한다.

```java
class Student {

    // ...

    @OneToMany(mappedBy = "student")
    Set<CourseRegistration> registrations;

    // ...
}

class Course {

    // ...

    @OneToMany(mappedBy = "course")
    Set<CourseRegistration> registrations;

    // ...
}
```

이전에 관계를 구성했으므로 JPA가 해당 구성을 어디에서 찾을 수 있는지 알려주기만 하면 된다.

또한 이 솔루션을 사용하여 학생들이 과목을 평가하는 이전 문제를 해결할 수 있다. 그러나 직접 해야 하는 경우가 아니면 전용 기본 키를 만드는 것은 이상하게 느껴진다.

게다가 RDBMS 관점에서 보면, 두 개의 외래 키를 합치면 완벽한 복합 키가 되기 때문에 별로 의미가 없다. 복합 키에는 명확한 의미가 있다. 관계에서 어떤 엔티티를 연결하는지이다.

그렇지 않으면, 이 두 가지 구현 중 하나를 선택하는 것은 종종 단순히 개인적인 선호도에 불과하다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-many-to-many](https://www.baeldung.com/jpa-many-to-many)
