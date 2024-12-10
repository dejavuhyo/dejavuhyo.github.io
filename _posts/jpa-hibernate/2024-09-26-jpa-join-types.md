---
title: JPA 조인 유형
author: dejavuhyo
date: 2024-09-26 10:34:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-join-type, jpa-join, jpa-조인-유형, jpa-조인]
---

## 1. 샘플 데이터 모델
예제에서 사용할 샘플 데이터 모델이다.

먼저 Employee 엔터티를 만든다.

```java
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    private String name;

    private int age;

    @ManyToOne
    private Department department;

    @OneToMany(mappedBy = "employee")
    private List<Phone> phones;

    // getters and setters...
}
```

각 직원은 단 하나의 부서에만 배정된다.

```java
@Entity
public class Department {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    private String name;

    @OneToMany(mappedBy = "department")
    private List<Employee> employees;

    // getters and setters...
}
```

마지막으로, 각 직원은 여러 개의 전화 번호를 갖게 된다.

```java
@Entity
public class Phone {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    private String number;

    @ManyToOne
    private Employee employee;

    // getters and setters...
}
```

## 2. 내부 조인
두 개 이상의 엔터티가 내부 조인되면 조인 조건과 일치하는 레코드만 결과에 수집된다.

### 1) 단일 값 연관 탐색 사용
내부 조인은 암묵적 일 수 있다. 이름에서 알 수 있듯이 개발자는 암묵적 내부 조인을 지정하지 않는다. 단일 값 연결을 탐색할 때마다 JPA는 자동으로 암묵적 조인을 만든다.

```java
@Test
public void whenPathExpressionIsUsedForSingleValuedAssociation_thenCreatesImplicitInnerJoin() {
    TypedQuery<Department> query
      = entityManager.createQuery(
          "SELECT e.department FROM Employee e", Department.class);
    List<Department> resultList = query.getResultList();
    
    // Assertions...
}
```

여기서 Employee 엔터티는 Department 엔터티와 다대일 관계를 갖는다. Employee 엔터티에서 Department로 이동하여 e.department를 지정하면 단일 값 연결을 탐색하게 된다. 결과적으로 JPA는 내부 조인을 만든다. 또한 조인 조건은 매핑 메타데이터에서 파생된다.

### 2) 단일 값 연결 사용
JPQL 쿼리에서 JOIN 키워드를 사용하는 명시적 내부 조인이다.

```java
@Test
public void whenJoinKeywordIsUsed_thenCreatesExplicitInnerJoin() {
    TypedQuery<Department> query
      = entityManager.createQuery(
          "SELECT d FROM Employee e JOIN e.department d", Department.class);
    List<Department> resultList = query.getResultList();
    
    // Assertions...
}
```

이 쿼리에서 JOIN 키워드와 FROM 절에 연관된 Department 엔터티를 지정했지만, 이전 쿼리에서는 전혀 지정하지 않았다. 그러나 이 구문적 차이를 제외하면 결과 SQL 쿼리는 매우 유사하다.

또한 선택적으로 INNER 키워드를 지정할 수 있다.

```java
@Test
public void whenInnerJoinKeywordIsUsed_thenCreatesExplicitInnerJoin() {
    TypedQuery<Department> query
      = entityManager.createQuery(
          "SELECT d FROM Employee e INNER JOIN e.department d", Department.class);
    List<Department> resultList = query.getResultList();

    // Assertions...
}
```

그러면 JPA가 자동으로 암묵적 내부 조인을 생성하므로, JPA는 경로 표현식을 지정할 때만 암묵적 내부 조인을 생성한다. 예를 들어, Department가 있는 Employee만 선택 하고 e.department와 같은 경로 표현식을 사용하지 않으려면 쿼리에서 JOIN 키워드를 사용해야 한다.

명확하게 말하면 무슨 일이 일어나고 있는지 더 쉽게 알 수 있다.

### 3) 컬렉션 값 연결 사용
명확하게 설명해야 할 또 다른 부분은 컬렉션 값 연관 관계이다.

데이터 모델을 살펴보면 Employee는 Phone과 일대다 관계를 가지고 있다. 이전 예에서와 같이 비슷한 쿼리를 작성해 볼 수 있다.

```sql
SELECT e.phones FROM Employee e
```

하지만, 이것은 의도한 대로 작동하지 않을 것 이다. 선택된 연관 e.phones가 컬렉션 값이므로 Phone 엔티티 대신 Collection 목록을 받게 된다.

```java
@Test
public void whenCollectionValuedAssociationIsSpecifiedInSelect_ThenReturnsCollections() {
    TypedQuery<Collection> query 
      = entityManager.createQuery(
          "SELECT e.phones FROM Employee e", Collection.class);
    List<Collection> resultList = query.getResultList();

    //Assertions
}
```

게다가 WHERE 절에서 Phone 엔터티를 필터링하고 싶다면 JPA는 허용하지 않는다. 이는 경로 표현식이 컬렉션 값 연관에서 계속될 수 없기 때문이다. 예를 들어 e.phones.number는 유효하지 않다.

대신, 명시적 내부 조인을 만들고 Phone 엔터티에 대한 별칭을 만들어야 한다. 그런 다음 SELECT 또는 WHERE 절에서 Phone 엔터티를 지정할 수 있다.

```java
@Test
public void whenCollectionValuedAssociationIsJoined_ThenCanSelect() {
    TypedQuery<Phone> query 
      = entityManager.createQuery(
          "SELECT ph FROM Employee e JOIN e.phones ph WHERE ph LIKE '1%'", Phone.class);
    List<Phone> resultList = query.getResultList();
    
    // Assertions...
}
```

## 3. 외부 조인
두 개 이상의 엔터티가 외부 조인된 경우 조인 조건을 만족하는 레코드와 왼쪽 엔터티의 레코드가 결과에 수집된다.

```java
@Test
public void whenLeftKeywordIsSpecified_thenCreatesOuterJoinAndIncludesNonMatched() {
    TypedQuery<Department> query 
      = entityManager.createQuery(
          "SELECT DISTINCT d FROM Department d LEFT JOIN d.employees e", Department.class);
    List<Department> resultList = query.getResultList();

    // Assertions...
}
```

여기의 결과에는 Employees과 연관된 Departments와 Employees이 없는 Departments가 포함된다.

이를 left outer join 이라고도 한다. JPA는 오른쪽 엔터티에서 일치하지 않는 레코드를 수집하는 오른쪽 조인을 제공하지 않는다. 하지만 FROM 절에서 엔터티를 바꿔 오른쪽 조인을 시뮬레이션할 수 있다.

## 4. WHERE 절의 조인

### 1) 조건이 있음
FROM 절에 두 엔터티를 나열한 다음 WHERE 절에 조인 조건을 지정할 수 있다.

특히 데이터베이스 수준의 외래 키가 없는 경우에 이 기능이 유용할 수 있다.

```java
@Test
public void whenEntitiesAreListedInFromAndMatchedInWhere_ThenCreatesJoin() {
    TypedQuery<Department> query 
      = entityManager.createQuery(
          "SELECT d FROM Employee e, Department d WHERE e.department = d", Department.class);
    List<Department> resultList = query.getResultList();
    
    // Assertions...
}
```

여기에서는 Employee 및 Department 엔터티를 결합하지만, 이번에는 WHERE 절에 조건을 지정한다.

### 2) 조건 없음(Cartesian Product)
마찬가지로, 조인 조건을 지정하지 않고도 FROM 절에 두 개의 엔터티를 나열할 수 있다. 이 경우, 데카르트 곱을 다시 얻게 된다. 즉, 첫 번째 엔터티의 모든 레코드가 두 번째 엔터티의 다른 모든 레코드와 쌍을 이룬다.

```java
@Test
public void whenEntitiesAreListedInFrom_ThenCreatesCartesianProduct() {
    TypedQuery<Department> query
      = entityManager.createQuery(
          "SELECT d FROM Employee e, Department d", Department.class);
    List<Department> resultList = query.getResultList();
    
    // Assertions...
}
```

추측할 수 있듯이 이런 종류의 쿼리는 좋은 성과를 거두지 못한다.

## 5. Multiple Joins
지금까지 두 개의 엔티티를 사용하여 조인을 수행했지만, 이는 규칙이 아니다. 또한 단일 JPQL 쿼리에서 여러 엔티티를 조인할 수 있다.

```java
@Test
public void whenMultipleEntitiesAreListedWithJoin_ThenCreatesMultipleJoins() {
    TypedQuery<Phone> query
      = entityManager.createQuery(
          "SELECT ph FROM Employee e
      JOIN e.department d
      JOIN e.phones ph
      WHERE d.name IS NOT NULL", Phone.class);
    List<Phone> resultList = query.getResultList();
    
    // Assertions...
}
```

여기서 Department가 있는 모든 Employees의 모든 Phones를 선택한다. 다른 내부 조인과 마찬가지로 JPA가 매핑 메타데이터에서 이 정보를 추출하기 때문에 조건을 지정하지 않는다.

## 6. Fetch Joins
페치 조인 주요 용도는 현재 쿼리에 대해 레이지 로드된 연결을 fetching 하는 것이다.

여기서 Employees를 열심히 로드할 것이다.

```java
@Test
public void whenFetchKeywordIsSpecified_ThenCreatesFetchJoin() {
    TypedQuery<Department> query 
      = entityManager.createQuery(
          "SELECT d FROM Department d JOIN FETCH d.employees", Department.class);
    List<Department> resultList = query.getResultList();
    
    // Assertions...
}
```

이 쿼리는 다른 쿼리와 매우 유사해 보이지만, 한 가지 차이점이 있다. Employee는 즉시 로드된다. 즉, 위의 테스트에서 getResultList를 호출하면 Department 엔터티에 employees 필드가 로드되어 데이터베이스로 다시 이동할 필요가 없다.

그러나 메모리 트레이드오프를 알고 있어야 한다. 단 하나의 쿼리만 수행했기 때문에 더 효율적일 수 있지만, 모든 Departments와 employees을 한 번에 메모리에 로드했다.

또한 외부 조인과 비슷한 방식으로 외부 페치 조인을 수행할 수 있다. 여기서는 조인 조건과 일치하지 않는 왼쪽 엔터티에서 레코드를 수집한다. 또한 지정된 연결을 로드한다.

```java
@Test
public void whenLeftAndFetchKeywordsAreSpecified_ThenCreatesOuterFetchJoin() {
    TypedQuery<Department> query 
      = entityManager.createQuery(
          "SELECT d FROM Department d LEFT JOIN FETCH d.employees", Department.class);
    List<Department> resultList = query.getResultList();
    
    // Assertions...
}
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-join-types](https://www.baeldung.com/jpa-join-types)
