---
title: Hibernate/JPA 일괄 Insert/Update
author: dejavuhyo
date: 2024-10-11 09:22:00 +0900
categories: [Framework, JPA/Hibernate]
tags: [batch-insert, batch-update, hibernate-insert-update, jpa-insert-update]
---

## 1. 설정

### 1) 샘플 데이터 모델
예제에서 사용할 샘플 데이터 모델이다.

먼저, School 엔티티를 생성한다.

```java
@Entity
public class School {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private long id;

    private String name;

    @OneToMany(mappedBy = "school")
    private List<Student> students;

    // Getters and setters...
}
```

각 학교에는 0명 이상의 Student가 있다.

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE)
    private long id;

    private String name;

    @ManyToOne
    private School school;

    // Getters and setters...
}
```

### 2) SQL 쿼리 추적
예제를 실행할 때 insert/update 문이 실제로 배치로 전송되는지 확인해야 한다. 안타깝게도 Hibernate log statements에서 SQL 문이 배치되었는지 여부를 알 수 없다. 결과적으로 데이터 소스 프록시를 사용하여 Hibernate/JPA SQL 문을 추적한다.

```java
private static class ProxyDataSourceInterceptor implements MethodInterceptor {
    private final DataSource dataSource;
    public ProxyDataSourceInterceptor(final DataSource dataSource) {
        this.dataSource = ProxyDataSourceBuilder.create(dataSource)
            .name("Batch-Insert-Logger")
            .asJson().countQuery().logQueryToSysOut().build();
    }
    
    // Other methods...
}
```

## 2. 기본 동작
Hibernate는 기본적으로 일괄 처리를 활성화하지 않는다. 즉, 각 insert/update 작업에 대해 별도의 SQL 문을 보낸다.

```java
@Transactional
@Test
public void whenNotConfigured_ThenSendsInsertsSeparately() {
    for (int i = 0; i < 10; i++) {
        School school = createSchool(i);
        entityManager.persist(school);
    }
    entityManager.flush();
}
```

여기서 10개의 School 엔티티를 유지했다. 쿼리 로그를 살펴보면 Hibernate가 각 insert 문을 별도로 전송하는 것을 볼 수 있다.

```text
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School1","1"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School2","2"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School3","3"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School4","4"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School5","5"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School6","6"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School7","7"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School8","8"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School9","9"]]
"querySize":1, "batchSize":0, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School10","10"]]
```

따라서 Hibernate를 구성하여 배칭을 활성화해야 한다. 이를 위해 `hibernate.jdbc.batch_size` 속성을 0보다 큰 숫자로 설정해야 한다.

EntityManager를 수동으로 생성하는 경우 Hibernate 속성에 `hibernate.jdbc.batch_size`를 추가해야 한다.

```java
public Properties hibernateProperties() {
    Properties properties = new Properties();
    properties.put("hibernate.jdbc.batch_size", "5");
    
    // Other properties...
    return properties;
}
```

Spring Boot를 사용한다면 애플리케이션 속성으로 정의할 수 있다.

```properties
spring.jpa.properties.hibernate.jdbc.batch_size=5
```

## 3. 단일 테이블에 대한 일괄 삽입

### 1) 명시적 플러시 없이 일괄 삽입
먼저, 하나의 엔터티 유형만 다루는 경우 일괄 삽입을 어떻게 사용할 수 있는지 살펴본다.

이전 코드 샘플을 사용하지만 이번에는 일괄 처리가 활성화되어 있다.

```java
@Transactional
@Test
public void whenInsertingSingleTypeOfEntity_thenCreatesSingleBatch() {
    for (int i = 0; i < 10; i++) {
        School school = createSchool(i);
        entityManager.persist(school);
    }
}
```

여기서 10개의 School 엔티티를 유지했다. 로그를 살펴보면 Hibernate가 일괄적으로 삽입 문을 보내는 것을 확인할 수 있다.

```text
"batch":true, "querySize":1, "batchSize":5, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School1","1"],["School2","2"],["School3","3"],["School4","4"],["School5","5"]]
"batch":true, "querySize":1, "batchSize":5, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School6","6"],["School7","7"],["School8","8"],["School9","9"],["School10","10"]]
```

여기서 중요한 사항 하나는 메모리 소비이다. 엔티티를 지속할 때 Hibernate는 그것을 지속성 컨텍스트에 저장한다. 예를 들어, 한 트랜잭션에서 100,000개의 엔티티를 지속하면 메모리에 100,000개의 엔티티 인스턴스가 있게 되어 OutOfMemoryException이 발생할 수 있다.

### 2) 명시적 플러시를 사용한 일괄 삽입
배치 작업 중에 메모리 사용을 최적화하는 방법이다. 지속성 컨텍스트의 역할을 자세히 살펴본다.

우선, 지속성 컨텍스트는 새로 생성되고 수정된 엔터티를 메모리에 저장한다. Hibernate는 트랜잭션이 동기화될 때 이러한 변경 사항을 데이터베이스로 보낸다. 이는 일반적으로 트랜잭션이 끝날 때 발생한다. 그러나 `EntityManager.flush()`를 호출하면 트랜잭션 동기화도 트리거된다.

둘째, 지속성 컨텍스트는 엔티티 캐시 역할을 하며, 1차 캐시라고도 한다. 지속성 컨텍스트에서 엔티티를 지우려면 `EntityManager.clear()`를 호출할 수 있다.

따라서 배칭 중 메모리 부하를 줄이기 위해 배치 크기에 도달할 때마다 애플리케이션 코드에서 `EntityManager.flush()` 및 `EntityManager.clear()`를 호출할 수 있다.

```java
@Transactional
@Test
public void whenFlushingAfterBatch_ThenClearsMemory() {
    for (int i = 0; i < 10; i++) {
        if (i > 0 && i % BATCH_SIZE == 0) {
            entityManager.flush();
            entityManager.clear();
        }
        School school = createSchool(i);
        entityManager.persist(school);
    }
}
```

여기서 지속성 컨텍스트에서 엔티티를 플러싱하여 Hibernate가 데이터베이스에 쿼리를 보내도록 한다. 또한 지속성 컨텍스트를 지우면 School 엔티티를 메모리에서 제거한다. 일괄 처리 동작은 동일하게 유지된다.

## 4. 여러 테이블에 대한 일괄 삽입
이제 하나의 트랜잭션에서 여러 엔터티 유형을 처리할 때 일괄 삽입을 구성하는 방법이다.

여러 유형의 엔티티를 지속시키고 싶을 때, Hibernate는 각 엔티티 유형에 대해 다른 배치를 생성한다. 이는 단일 배치에 하나의 유형의 엔티티만 있을 수 있기 때문이다.

또한, Hibernate가 insert 문을 수집할 때 현재 배치의 엔티티 유형과 다른 엔티티 유형을 만날 때마다 새 배치를 만든다. 이는 해당 엔티티 유형에 대한 배치가 이미 있는 경우에도 마찬가지이다.

```java
@Transactional
@Test
public void whenThereAreMultipleEntities_ThenCreatesNewBatch() {
    for (int i = 0; i < 10; i++) {
        if (i > 0 && i % BATCH_SIZE == 0) {
            entityManager.flush();
            entityManager.clear();
        }
        School school = createSchool(i);
        entityManager.persist(school);
        Student firstStudent = createStudent(school);
        Student secondStudent = createStudent(school);
        entityManager.persist(firstStudent);
        entityManager.persist(secondStudent);
    }
}
```

여기서는 School을 삽입하고, 두 개의 Student를 할당한 다음 이 과정을 10번 반복한다.

로그에서 Hibernate가 크기 1의 여러 배치로 School 삽입 문을 보내는 것을 볼 수 있는데, 크기 5의 배치 2개만 예상했다. 게다가 Student 삽입 문도 크기 5의 배치 4개 대신 크기 2의 여러 배치로 전송되었다.

```text
"batch":true, "querySize":1, "batchSize":1, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School1","1"]]
"batch":true, "querySize":1, "batchSize":2, "query":["insert into student (name, school_id, id) 
  values (?, ?, ?)"], "params":[["Student-School1","1","2"],["Student-School1","1","3"]]
"batch":true, "querySize":1, "batchSize":1, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School2","4"]]
"batch":true, "querySize":1, "batchSize":2, "query":["insert into student (name, school_id, id) 
  values (?, ?, ?)"], "params":[["Student-School2","4","5"],["Student-School2","4","6"]]
"batch":true, "querySize":1, "batchSize":1, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School3","7"]]
"batch":true, "querySize":1, "batchSize":2, "query":["insert into student (name, school_id, id) 
  values (?, ?, ?)"], "params":[["Student-School3","7","8"],["Student-School3","7","9"]]
Other log lines...
```

동일한 엔티티 유형의 모든 삽입 문을 일괄 처리하려면 `hibernate.order_inserts` 속성을 구성해야 한다.

EntityManagerFactory를 사용하여 Hibernate 속성을 수동으로 구성할 수 있다.

```java
public Properties hibernateProperties() {
    Properties properties = new Properties();
    properties.put("hibernate.order_inserts", "true");
    
    // Other properties...
    return properties;
}
```

Spring Boot를 사용하는 경우 `application.properties`에서 속성을 구성할 수 있다.

```properties
spring.jpa.properties.hibernate.order_inserts=true
```

이 속성을 추가하면 School 삽입에 대한 배치 1개와 Student 삽입에 대한 배치 2개가 생성된다.

```text
"batch":true, "querySize":1, "batchSize":5, "query":["insert into school (name, id) values (?, ?)"], 
  "params":[["School6","16"],["School7","19"],["School8","22"],["School9","25"],["School10","28"]]
"batch":true, "querySize":1, "batchSize":5, "query":["insert into student (name, school_id, id) 
  values (?, ?, ?)"], "params":[["Student-School6","16","17"],["Student-School6","16","18"],
  ["Student-School7","19","20"],["Student-School7","19","21"],["Student-School8","22","23"]]
"batch":true, "querySize":1, "batchSize":5, "query":["insert into student (name, school_id, id) 
  values (?, ?, ?)"], "params":[["Student-School8","22","24"],["Student-School9","25","26"],
  ["Student-School9","25","27"],["Student-School10","28","29"],["Student-School10","28","30"]]
```

## 5. 일괄 업데이트
일괄 삽입과 비슷하게, 여러 업데이트 문장을 그룹화하여 한 번에 데이터베이스로 보낼 수 있다.

이를 활성화하려면 `hibernate.order_updates` 및 `hibernate.batch_versioned_data` 속성을 구성한다.

EntityManagerFactory를 수동으로 생성하는 경우 프로그래밍 방식으로 속성을 설정할 수 있다.

```java
public Properties hibernateProperties() {
    Properties properties = new Properties();
    properties.put("hibernate.order_updates", "true");
    properties.put("hibernate.batch_versioned_data", "true");
    
    // Other properties...
    return properties;
}
```

Spring Boot를 사용한다면 `application.properties`에 추가하면 된다.

```properties
spring.jpa.properties.hibernate.order_updates=true
spring.jpa.properties.hibernate.batch_versioned_data=true
```

이러한 속성을 구성한 후 Hibernate는 업데이트 문을 일괄적으로 그룹화해야 한다.

```java
@Transactional
@Test
public void whenUpdatingEntities_thenCreatesBatch() {
    TypedQuery<School> schoolQuery = 
      entityManager.createQuery("SELECT s from School s", School.class);
    List<School> allSchools = schoolQuery.getResultList();
    for (School school : allSchools) {
        school.setName("Updated_" + school.getName());
    }
}
```

여기서 학교 엔티티를 업데이트했고 Hibernate는 크기가 5인 2개의 배치로 SQL 문을 전송한다.

```text
"batch":true, "querySize":1, "batchSize":5, "query":["update school set name=? where id=?"], 
  "params":[["Updated_School1","1"],["Updated_School2","2"],["Updated_School3","3"],
  ["Updated_School4","4"],["Updated_School5","5"]]
"batch":true, "querySize":1, "batchSize":5, "query":["update school set name=? where id=?"], 
  "params":[["Updated_School6","6"],["Updated_School7","7"],["Updated_School8","8"],
  ["Updated_School9","9"],["Updated_School10","10"]]
```

## 6. @Id 생성 전략
삽입에 배치를 사용하고 싶을 때, 기본 키 생성 전략을 알고 있어야 한다. 엔티티가 `GenerationType.IDENTITY` 식별자 생성기를 사용하면 Hibernate는 배치 삽입을 자동으로 비활성화한다.

예제의 엔터티는 `GenerationType.SEQUENCE` 식별자 생성기를 사용하므로 Hibernate는 일괄 작업을 가능하게 한다.

```java
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE)
private long id;
```

## [출처 및 참고]
* [https://www.baeldung.com/jpa-hibernate-batch-insert-update](https://www.baeldung.com/jpa-hibernate-batch-insert-update)
