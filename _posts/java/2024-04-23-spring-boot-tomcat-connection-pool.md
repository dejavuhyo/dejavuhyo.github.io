---
title: Spring Boot Tomcat Connection Pool
author: dejavuhyo
date: 2024-04-23 14:44:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-tomcat, tomcat-connection-pool, connection-pool]
---

## 1. Maven 종속성
Spring Boot는 놀라운 성능과 기업용 기능으로 인해 HikariCP를 기본 연결 풀로 사용한다.

Spring Boot가 연결 풀 데이터 소스를 자동으로 구성하는 방법은 다음과 같습니다.

* Spring Boot는 클래스 경로에서 HikariCP를 찾고 존재하는 경우 기본적으로 사용한다.

* 클래스 경로에서 HikariCP를 찾을 수 없으면 Spring Boot는 사용 가능한 경우 Tomcat JDBC 연결 풀을 선택한다.

* 이러한 옵션 중 어느 것도 사용할 수 없는 경우 Spring Boot는 사용 가능한 경우 Apache Commons DBCP2를 선택한다.

기본 HikariCP 대신 Tomcat JDBC 연결 풀을 구성하기 위해 spring-boot-starter-data-jpa 종속성에서 HikariCP를 제외 하고 [tomcat-jdbc](https://mvnrepository.com/artifact/org.apache.tomcat/tomcat-jdbc) Maven 종속성을 `pom.xml`에 추가한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
    <exclusions>
        <exclusion>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>tomcat-jdbc</artifactId>
    <version>10.1.7</version>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>2.1.214</version>
    <scope>runtime</scope>
</dependency>
```

이 간단한 접근 방식을 사용하면 `@Configuration` 클래스를 작성하고 프로그래밍 방식으로 DataSource Bean을 정의할 필요 없이 Tomcat 연결 풀을 사용하여 Spring Boot를 얻을 수 있다.

이 경우 H2 인메모리 데이터베이스를 사용하고 있다. Spring Boot는 데이터베이스 URL, 사용자 및 비밀번호를 지정하지 않고도 H2를 자동 구성한다.

`pom.xml` 파일에 해당 종속성을 포함하기만 하면 Spring Boot가 나머지 작업을 자동으로 수행한다.

또는 Spring Boot가 사용하는 연결 풀 검색 알고리즘을 건너뛰고 `spring.datasource.type` 속성을 사용하여 `application.properties` 파일에 연결 풀링 데이터 소스를 명시적으로 지정할 수 있다.

```properties
spring.datasource.type=org.apache.tomcat.jdbc.pool.DataSource
// other spring datasource properties
```

## 2. application.properties 파일을 사용하여 Connection Pool 조정
Spring Boot에서 Tomcat 연결 풀을 성공적으로 구성한 후에는 성능을 최적화하고 특정 요구 사항에 맞게 몇 가지 추가 속성을 설정해야 할 가능성이 높다.

`application.properties` 파일에서 설정할 수 있다.

```properties
spring.datasource.tomcat.initial-size=15
spring.datasource.tomcat.max-wait=20000
spring.datasource.tomcat.max-active=50
spring.datasource.tomcat.max-idle=15
spring.datasource.tomcat.min-idle=8
spring.datasource.tomcat.default-auto-commit=true
```

풀의 초기 크기, 최대 및 최소 유휴 연결 수와 같은 몇 가지 추가 연결 풀링 속성을 구성했다.

우리는 또한 일부 Hibernate 특정 속성을 지정할 수 있다.

```properties
# Hibernate specific properties
spring.jpa.show-sql=false
spring.jpa.hibernate.ddl-auto=update
spring.jpa.hibernate.naming-strategy=org.hibernate.cfg.ImprovedNamingStrategy
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect
spring.jpa.properties.hibernate.id.new_generator_mappings=false
```

## 3. Connection Pool 테스트
Spring Boot가 연결 풀을 올바르게 구성했는지 확인하기 위해 간단한 통합 테스트를 작성한다.

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringBootTomcatConnectionPoolIntegrationTest {
    
    @Autowired
    private DataSource dataSource;
    
    @Test
    public void givenTomcatConnectionPoolInstance_whenCheckedPoolClassName_thenCorrect() {
        assertThat(dataSource.getClass().getName())
          .isEqualTo("org.apache.tomcat.jdbc.pool.DataSource");
    }
}
```

## 4. 샘플 Command Line Application
모든 연결 풀링 배관이 이미 설정되었으므로 간단한 명령줄 애플리케이션을 구축한다.

이를 통해 Spring Data JPA (및 전이적으로 Spring Boot)가 즉시 제공하는 강력한 DAO 계층을 사용하여 H2 데이터베이스에서 일부 CRUD 작업을 수행하는 방법을 확인할 수 있다.

### 1) Customer Entity Class
먼저 Customer 엔터티 클래스를 정의한다.

```java
@Entity
@Table(name = "customers")
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;
    @Column(name = "first_name")
    private String firstName;
    
    // standard constructors / getters / setters / toString
}
```

### 2) CustomerRepository Interface
이 경우에는 몇 가지 Customer 엔터티에 대해 CRUD 작업을 수행하려고 한다. 또한 특정 성과 일치하는 모든 고객을 가져와야 한다.

따라서 Spring Data JPA의 CrudRepository 인터페이스를 확장하고 맞춤형 메소드를 정의한다.

```java
public interface CustomerRepository extends CrudRepository<Customer, Long> {
    List<Customer> findByLastName(String lastName);
}
```

이제 last name으로 Customer 엔터티를 쉽게 가져올 수 있다.

### 3) CommandLineRunner Implementation
마지막으로 최소한 몇 개의 Customer 엔터티를 데이터베이스에 유지하고 Tomcat 연결 풀이 실제로 작동하는지 확인해야 한다.

Spring Boot의 CommandLineRunner 인터페이스 구현을 만든다. Spring Boot는 애플리케이션을 시작하기 전에 구현을 부트스트랩한다.

```java
public class CommandLineCrudRunner implements CommandLineRunner {
    
    private static final Logger logger = LoggerFactory.getLogger(CommandLineCrudRunner.class);
    
    @Autowired
    private final CustomerRepository repository;
    
    public void run(String... args) throws Exception {
        repository.save(new Customer("John", "Doe"));
        repository.save(new Customer("Jennifer", "Wilson"));
        
        logger.info("Customers found with findAll():");
        repository.findAll().forEach(c -> logger.info(c.toString()));
        
        logger.info("Customer found with findById(1L):");
        Customer customer = repository.findById(1L)
          .orElseGet(() -> new Customer("Non-existing customer", ""));
        logger.info(customer.toString());
        
        logger.info("Customer found with findByLastName('Wilson'):");
        repository.findByLastName("Wilson").forEach(c -> {
            logger.info(c.toString());
        });
    }
}
```

간단히 말해서 CommandLineCrudRunner 클래스는 먼저 몇 가지 Customer 엔터티를 데이터베이스에 저장한다. 다음으로 `findById()` 메서드를 사용하여 첫 번째 항목을 가져온다. 마지막으로 `findByLastName()` 메소드를 사용하여 고객을 검색한다.

### 4) 스프링 부트 애플리케이션 실행
마지막으로 해야 할 일은 샘플 애플리케이션을 실행하는 것이다. 그런 다음 Spring Boot/Tomcat 연결 풀이 실제로 작동하는 것을 볼 수 있다.

```java
@SpringBootApplication
public class SpringBootConsoleApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(SpringBootConsoleApplication.class);
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-tomcat-connection-pool](https://www.baeldung.com/spring-boot-tomcat-connection-pool)
