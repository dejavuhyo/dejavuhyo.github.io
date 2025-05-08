---
title: JPA 쿼리 매개변수
author: dejavuhyo
date: 2024-09-24 09:12:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [jpa-query-parameter, jap-query, query-parameter, jpa-쿼리, 쿼리-매개변수]
---

## 1. 쿼리 매개변수란
쿼리 매개변수는 매개변수화된 쿼리를 빌드하고 실행하는 방법이다.

```sql
SELECT * FROM employees e WHERE e.emp_number = '123';
```

이렇게 할 것이다.

```sql
SELECT * FROM employees e WHERE e.emp_number = ?;
```

JDBC 준비된 명령문을 사용하려면 쿼리를 실행하기 전에 매개변수를 설정해야 한다.

```java
pStatement.setString(1, 123);
```

## 2. 매개변수를 사용 이유
쿼리 매개변수를 사용하는 대신 리터럴을 사용할 수도 있지만, 이는 권장되는 방법은 아니다.

JPA API를 사용하여 emp_number로 직원을 가져오는 이전 쿼리를 다시 작성한다. 하지만 매개변수를 사용하는 대신 리터럴을 사용하여 상황을 명확하게 설명한다.

```java
String empNumber = "A123";
TypedQuery<Employee> query = em.createQuery(
  "SELECT e FROM Employee e WHERE e.empNumber = '" + empNumber + "'", Employee.class);
Employee employee = query.getSingleResult();
```

이 접근 방식에는 몇 가지 단점이 있다.

* 매개변수를 임베딩하면 보안 위험이 발생하여 JPQL 주입 공격에 취약해진다. 예상 값 대신 공격자는 예상치 못한 위험할 수 있는 JPQL 표현식을 주입할 수 있다.

* 사용하는 JPA 구현과 애플리케이션의 휴리스틱에 따라 쿼리 캐시가 고갈될 수 있다. 새 value/parameter와 함께 사용할 때마다 새 쿼리가 빌드, 컴파일 및 캐시될 수 있다. 최소한 효율적이지 않을 것이고 예상치 못한 OutOfMemoryError로 이어질 수도 있다.

## 3. JPA 쿼리 매개변수
JDBC 준비된 명령문 매개변수와 유사하게 JPA는 다음을 사용하여 매개변수화된 쿼리를 작성하는 두 가지 방법을 지정합니다.

* Positional 매개변수

* Named 매개변수

positional 매개변수나 named 매개변수를 모두 사용할 수 있지만, 동일한 쿼리 내에서 이 두 매개변수를 섞어서 사용해서는 안된다.

### 1) Positional 매개변수
위치 매개변수를 사용하는 것은 앞서 나열된 문제를 피하는 한 가지 방법이다.

```java
TypedQuery<Employee> query = em.createQuery(
  "SELECT e FROM Employee e WHERE e.empNumber = ?1", Employee.class);
String empNumber = "A123";
Employee employee = query.setParameter(1, empNumber).getSingleResult();
```

이전 예제에서 보았듯이, 물음표를 입력한 다음 양의 정수를 입력하여 쿼리 내에서 이러한 매개변수를 선언한다. 1 부터 시작 하여 앞으로 나아가며 매번 1씩 증가시킨다.

동일한 쿼리 내에서 동일한 매개변수를 두 번 이상 사용할 수 있으며, 이로 인해 이러한 매개변수는 명명된 매개변수와 더 유사해진다.

매개변수 번호 매기기는 사용성, 가독성, 유지관리를 개선해 주므로 매우 유용한 기능이다.

네이티브 SQL 쿼리도 위치 매개변수 바인딩을 지원한다.

### 2) Collection-Valued 위치 매개변수
또한 collection-valued 매개변수를 사용할 수 있다.

```java
TypedQuery<Employee> query = entityManager.createQuery(
  "SELECT e FROM Employee e WHERE e.empNumber IN (?1)" , Employee.class);
List<String> empNumbers = Arrays.asList("A123", "A124");
List<Employee> employees = query.setParameter(1, empNumbers).getResultList();
```

### 3) Named 매개변수
Named 매개변수는 위치 매개변수와 매우 유사하다. 그러나 이를 사용하면 매개변수를 더 명확하게 만들고 쿼리를 더 읽기 쉽게 만들 수 있다.

```java
TypedQuery<Employee> query = em.createQuery(
  "SELECT e FROM Employee e WHERE e.empNumber = :number" , Employee.class);
String empNumber = "A123";
Employee employee = query.setParameter("number", empNumber).getSingleResult();
```

이전 샘플 쿼리는 첫 번째 쿼리와 동일하지만, `?1` 대신 명명된 매개변수인 `:number`를 사용했다.

콜론으로 매개변수를 선언한 다음, 문자열 식별자(JPQL 식별자)를 붙인 것을 볼 수 있다. 이는 런타임에 설정할 실제 값의 플레이스홀더이다. 쿼리를 실행하기 전에 setParameter 메서드를 실행하여 매개변수를 설정해야 한다.

주목할 점은 TypedQuery가 메서드 체이닝을 지원한다는 것이다. 이 기능은 여러 매개변수를 설정해야 할 때 매우 유용하다.

두 개의 명명된 매개변수를 사용하여 이전 쿼리의 변형을 만들어 메서드 체이닝을 설명한다.

```java
TypedQuery<Employee> query = em.createQuery(
  "SELECT e FROM Employee e WHERE e.name = :name AND e.age = :empAge" , Employee.class);
String empName = "John Doe";
int empAge = 55;
List<Employee> employees = query
  .setParameter("name", empName)
  .setParameter("empAge", empAge)
  .getResultList();
```

여기서 주어진 이름과 나이를 가진 모든 직원을 검색한다. 분명히 보고 예상할 수 있듯이, 여러 매개변수와 필요한 만큼의 발생 횟수를 가진 쿼리를 빌드할 수 있다.

어떤 이유로 같은 쿼리 내에서 같은 매개변수를 여러 번 사용해야 하는 경우, "setParameter" 메서드를 실행하여 한 번만 설정하면 된다. 런타임에 지정된 값은 매개변수의 각 발생을 대체한다.

마지막으로, Java Persistence API 사양은 네이티브 쿼리가 명명된 매개변수를 지원하도록 명령하지 않는다. Hibernate와 같은 일부 구현이 이를 지원하더라도, 이를 사용한다면 쿼리가 이식성이 떨어질 것이다.

### 4) Collection-Valued 매개변수
명확하게 하기 위해 이것이 컬렉션 값 매개변수와 함께 어떻게 작동하는지 확인한다.

```java
TypedQuery<Employee> query = entityManager.createQuery(
  "SELECT e FROM Employee e WHERE e.empNumber IN (:numbers)" , Employee.class);
List<String> empNumbers = Arrays.asList("A123", "A124");
List<Employee> employees = query.setParameter("numbers", empNumbers).getResultList();
```

보시다시피 위치 매개변수와 비슷한 방식으로 작동한다.

## 4. 기준 쿼리 매개변수
JPA 쿼리는 JPA Criteria API를 사용하여 작성할 수 있으며, [Hibernate 공식 문서](https://docs.jboss.org/hibernate/orm/5.2/topical/html_single/metamodelgen/MetamodelGenerator.html)에서 이에 대해 자세히 설명하고 있다.

이 유형의 쿼리에서는 이름이나 인덱스 대신 객체를 사용하여 매개변수를 나타낸다.

이번에는 CriteriaQuery를 사용할 때 쿼리 매개변수를 처리하는 방법을 보여주기 위해 Criteria API를 사용하여 동일한 쿼리를 다시 작성한다.

```java
CriteriaBuilder cb = em.getCriteriaBuilder();

CriteriaQuery<Employee> cQuery = cb.createQuery(Employee.class);
Root<Employee> c = cQuery.from(Employee.class);
ParameterExpression<String> paramEmpNumber = cb.parameter(String.class);
cQuery.select(c).where(cb.equal(c.get(Employee_.empNumber), paramEmpNumber));

TypedQuery<Employee> query = em.createQuery(cQuery);
String empNumber = "A123";
query.setParameter(paramEmpNumber, empNumber);
Employee employee = query.getResultList();
```

이런 유형의 쿼리에서는 매개변수 객체를 사용하기 때문에 매개변수의 메커니즘이 약간 다르지만 본질적으로는 차이가 없다.

## [출처 및 참고]
* [https://www.baeldung.com/jpa-query-parameters](https://www.baeldung.com/jpa-query-parameters)
