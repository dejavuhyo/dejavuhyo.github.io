---
title: Hibernate Query Language
author: dejavuhyo
date: 2023-01-09 20:30:00 +0900
categories: [DevOps, JPA/Hibernate]
tags: [hibernate-query-language, hibernate, query-language, hibernate-query, 하이버네이트, 하이버네이트-쿼리, 하이버네이트-쿼리-언어, 쿼리-언어]
---

## 1. Query Language
HQL(Hibernate Query Language)은 SQL과 유사한 개체 지향 쿼리 언어이지만 테이블과 열에서 작동하는 대신 HQL은 영구 개체 및 해당 속성과 함께 작동한다. HQL 쿼리는 Hibernate에 의해 기존의 SQL 쿼리로 변환되며, 차례로 데이터베이스에서 작업을 수행한다.

Native SQL을 사용하여 Hibernate와 직접 SQL 문을 사용할 수 있지만 데이터베이스 이식성 번거로움을 피하고 Hibernate의 SQL 생성 및 캐싱 전략을 활용하기 위해 가능할 때마다 HQL을 사용하는 것이 좋다.

SELECT, FROM, WHERE 등과 같은 키워드는 대소문자를 구분하지 않지만, 테이블 및 열 이름과 같은 속성은 HQL에서 대소문자를 구분한다.

## 2. FROM 절
완전한 영구 객체를 메모리에 로드하려면 FROM 절을 사용한다. 다음은 FROM 절을 사용하는 간단한 구문이다.

```java
String hql = "FROM Employee";
Query query = session.createQuery(hql);
List results = query.list();
```

HQL에서 클래스 이름을 정규화해야 하는 경우 패키지 및 클래스 이름을 지정한다.

``java
String hql = "FROM com.hibernatebook.criteria.Employee";
Query query = session.createQuery(hql);
List results = query.list();
```

## 3. AS 절
AS 절은 특히 긴 쿼리가 있는 경우 HQL 쿼리의 클래스에 별칭을 할당하는데 사용할 수 있다.

```java
String hql = "FROM Employee AS E";
Query query = session.createQuery(hql);
List results = query.list();
```

AS 키워드는 선택 사항이며 다음과 같이 클래스 이름 바로 뒤에 별칭을 지정할 수도 있다.

```java
String hql = "FROM Employee E";
Query query = session.createQuery(hql);
List results = query.list();
```

## 4. SELECT 절
SELECT 절은 from 절보다 결과 집합에 대해 더 많은 제어를 제공한다. 완전한 개체 대신 개체의 몇 가지 속성을 얻으려면 SELECT 절을 사용한다.

다음은 Employee 개체의 first_name 필드를 얻기 위해 SELECT 절을 사용하는 간단한 구문이다. 여기서 Employee.firstName은 EMPLOYEE 테이블의 필드가 아니라 Employee 개체의 속성이라는 점에 주의한다.

```java
String hql = "SELECT E.firstName FROM Employee E";
Query query = session.createQuery(hql);
List results = query.list();
```

## 5. WHERE 절
저장소에서 반환되는 특정 개체의 범위를 좁히려면 WHERE 절을 사용한다.

```java
String hql = "FROM Employee E WHERE E.id = 10";
Query query = session.createQuery(hql);
List results = query.list();
```

## 6. ORDER BY 절
HQL 쿼리 결과를 정렬하려면 ORDER BY 절을 사용해야 한다. 오름차순(ASC) 또는 내림차순(DESC) 결과 집합의 개체에 대한 속성별로 결과를 정렬할 수 있다.

```java
String hql = "FROM Employee E WHERE E.id > 10 ORDER BY E.salary DESC";
Query query = session.createQuery(hql);
List results = query.list();
```

둘 이상의 속성을 기준으로 정렬하려면 order by 절 끝에 다음과 같이 추가 속성을 쉼표로 구분하여서 추가하면 된다.

```java
String hql = "FROM Employee E WHERE E.id > 10 " +
             "ORDER BY E.firstName DESC, E.salary DESC ";
Query query = session.createQuery(hql);
List results = query.list();
```

## 7. GROUP BY 절
Hibernate가 데이터베이스에서 정보를 가져오고 속성값을 기반으로 그룹화하고 일반적으로 결과를 사용하여 집계 값을 포함하도록 한다.

```java
String hql = "SELECT SUM(E.salary), E.firtName FROM Employee E " +
             "GROUP BY E.firstName";
Query query = session.createQuery(hql);
List results = query.list();
```

## 8. 명명된 매개변수 사용
Hibernate는 HQL 쿼리에서 명명된 매개변수를 지원한다. 이렇게 하면 사용자의 입력을 쉽게 받아들이는 HQL 쿼리를 작성할 수 있으며 SQL 삽입 공격을 방어할 필요가 없다.

```java
String hql = "FROM Employee E WHERE E.id = :employee_id";
Query query = session.createQuery(hql);
query.setParameter("employee_id",10);
List results = query.list();
```

## 9. UPDATE 절
대량 업데이트는 Hibernate 3에서 HQL에 새로 추가되었으며 Hibernate 2에서와는 다르게 Hibernate 3에서 작업을 삭제한다. 쿼리 인터페이스에는 이제 HQL UPDATE 또는 DELETE 문을 실행하기 위한 executeUpdate()라는 메서드가 포함되어 있다.

UPDATE 절은 하나 이상의 개체에 대한 하나 이상의 속성을 업데이트하는데 사용할 수 있다. 

```java
String hql = "UPDATE Employee set salary = :salary "  + 
             "WHERE id = :employee_id";
Query query = session.createQuery(hql);
query.setParameter("salary", 1000);
query.setParameter("employee_id", 10);
int result = query.executeUpdate();
System.out.println("Rows affected: " + result);
```

## 10. DELETE 절
DELETE 절을 사용하여 하나 이상의 개체를 삭제할 수 있다.

```java
String hql = "DELETE FROM Employee "  + 
             "WHERE id = :employee_id";
Query query = session.createQuery(hql);
query.setParameter("employee_id", 10);
int result = query.executeUpdate();
System.out.println("Rows affected: " + result);
```

## 11. INSERT 절
HQL은 한 개체에서 다른 개체로 레코드를 삽입할 수 있는 경우에만 INSERT INTO 절을 지원한다.

```java
String hql = "INSERT INTO Employee(firstName, lastName, salary)"  + 
             "SELECT firstName, lastName, salary FROM old_employee";
Query query = session.createQuery(hql);
int result = query.executeUpdate();
System.out.println("Rows affected: " + result);
```

## 12. Aggregate Methods
HQL은 SQL과 유사한 다양한 집계 방법을 지원한다. HQL에서 SQL과 동일한 방식으로 작동하며 다음은 사용할 수 있는 기능 목록이다.

| Functions | Description |
|:-----:|:-----:|
| `avg(property name)` | 속성 값의 평균 |
| `count(property name or *)` | 결과에서 속성이 발생한 횟수 |
| `max(property name)` | 속성 값의 최대값 |
| `min(property name)` | 속성 값의 최소값 |
| `sum(property name)` | 속성 값의 합계 |

distinct 키워드는 행 집합의 고유한 값만 계산한다.

```java
String hql = "SELECT count(distinct E.firstName) FROM Employee E";
Query query = session.createQuery(hql);
List results = query.list();
```

## 13. Pagination using Query
페이징을 위한 Query 인터페이스에는 두 가지 방법이 있다.

| Method | Description |
|:-----:|:-----:|
| `Query setFirstResult(int startPosition)` | 이 메서드는 결과 집합의 첫 번째 행을 나타내는 정수를 사용하며 행 0부터 시작한다. |
| `Query setMaxResults(int maxResult)` | 이 메서드는 Hibernate에서 개체의 고정 개수 maxResults를 검색하도록 한다. |

위의 두 가지 방법을 함께 사용하면 웹 또는 Swing 애플리케이션에서 페이징 구성 요소를 구성할 수 있다.

```java
String hql = "FROM Employee";
Query query = session.createQuery(hql);
query.setFirstResult(1);
query.setMaxResults(10);
List results = query.list();
```

## [출처 및 참고]
* [https://www.tutorialspoint.com/hibernate/hibernate_query_language.htm](https://www.tutorialspoint.com/hibernate/hibernate_query_language.htm)
