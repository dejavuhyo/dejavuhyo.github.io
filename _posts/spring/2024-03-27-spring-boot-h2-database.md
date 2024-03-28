---
title: Spring Boot H2 Database
author: dejavuhyo
date: 2024-03-27 14:02:00 +0900
categories: [Framework, Spring]
tags: [spring-database, spring-h2, h2-database, spring-boot-h2]
---

## 1. 종속성
[h2](https://mvnrepository.com/artifact/com.h2database/h2) 및 [spring-boot-starter-data-jpa](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-jpa) 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

## 2. 데이터베이스 구성
기본적으로 Spring Boot는 사용자 이름 sa와 빈 비밀번호를 사용하여 메모리 내 저장소에 연결하도록 애플리케이션을 구성한다.

그러나 `application.properties` 파일에 다음 속성을 추가하여 해당 매개변수를 변경할 수 있다.

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
```

또는 해당 속성을 `application.yaml` 파일에 추가하여 애플리케이션의 데이터베이스 구성에 YAML을 사용할 수도 있다.

```yml
spring:
  datasource:
    url: jdbc:h2:mem:mydb
    username: sa
    password: password
    driverClassName: org.h2.Driver
  jpa:
    spring.jpa.database-platform: org.hibernate.dialect.H2Dialect
```

설계상 인메모리 데이터베이스는 휘발성이므로 애플리케이션을 다시 시작한 후 데이터가 손실된다.

파일 기반 저장소를 사용하면 이러한 동작을 변경할 수 있다. 이렇게 하려면 `spring.datasource.url` 속성을 업데이트해야 한다.

```properties
spring.datasource.url=jdbc:h2:file:/data/demo
```

마찬가지로 `application.yaml`에서 파일 기반 스토리지에 대해 동일한 속성을 추가할 수 있다.

```yml
spring:
  datasource:
    url: jdbc:h2:file:/data/demo
```

데이터베이스는 다른 모드에서도 작동 할 수 있다.

## 3. 데이터베이스 운영
Spring Boot 내에서 H2를 사용하여 CRUD 작업을 수행하는 것은 다른 SQL 데이터베이스와 동일하다.

### 1) DataSource Initialization
기본 SQL 스크립트를 사용하여 데이터베이스를 초기화할 수 있다. 이를 설명하기 위해 `src/main/resources` 디렉터리에 `data.sql` 파일을 추가한다.

```sql
INSERT INTO countries (id, name) VALUES (1, 'USA');
INSERT INTO countries (id, name) VALUES (2, 'France');
INSERT INTO countries (id, name) VALUES (3, 'Brazil');
INSERT INTO countries (id, name) VALUES (4, 'Italy');
INSERT INTO countries (id, name) VALUES (5, 'Canada');
```

여기서 스크립트는 스키마의 국가 테이블을 일부 샘플 데이터로 채운다.

Spring Boot는 자동으로 이 파일을 선택하고 구성된 H2 인스턴스와 같은 내장된 메모리 내 데이터베이스에 대해 실행한다. 이는 테스트나 초기화 목적으로 데이터베이스를 시드하는 좋은 방법 이다.

`spring.sql.init.mode` 속성을 `never`로 설정하여 이 기본 동작을 비활성화할 수 있다. 또한 초기 데이터를 로드하도록 여러 SQL 파일을 구성할 수도 있다.

### 2) Hibernate 및 data.sql
기본적으로 `data.sql` 스크립트는 Hibernate 초기화 전에 실행된다. 이는 스크립트 기반 초기화를 [Flyway](https://www.baeldung.com/database-migrations-with-flyway) 및 [Liquibase](https://www.baeldung.com/liquibase-refactor-schema-of-java-app)와 같은 다른 데이터베이스 마이그레이션 도구와 일치시킨다. 매번 Hibernate에 의해 생성된 스키마를 다시 생성할 때 추가 속성을 설정해야 한다.

```properties
spring.jpa.defer-datasource-initialization=true
```

이는 기본 Spring Boot 동작을 수정하고 Hibernate에 의해 스키마가 생성된 후 데이터를 채운다. 게다가, `data.sql`로 채우기 전에 Hibernate가 생성한 스키마를 기반으로 구축하기 위해 `Schema.sql` 스크립트를 사용할 수도 있다. 그러나 서로 다른 스키마 생성 메커니즘을 혼합하는 것은 권장되지 않는다.

## 4. H2 Console 접근
H2 데이터베이스에는 데이터베이스 내용을 탐색하고 SQL 쿼리를 실행하기 위한 GUI 콘솔이 내장되어 있다. 기본적으로 H2 콘솔은 Spring에서 활성화되지 않는다.

이를 활성화하려면 `application.properties`에 다음 속성을 추가해야 한다.

```properties
spring.h2.console.enabled=true
```

YAML 구성을 사용하는 경우 `application.yaml`에 속성을 추가해야 한다.

```yml
spring:
  h2:
    console.enabled: true
```

그런 다음 애플리케이션을 시작한 후 `http://localhost:8080/h2-console`로 이동하면 로그인 페이지가 표시된다.

로그인 페이지에서는 `application.properties`에서 사용한 것과 동일한 자격 증명을 제공한다.

![login](/assets/img/2024-03-27-spring-boot-h2-database/login.png)

연결하면 페이지 왼쪽에 모든 테이블이 나열된 포괄적인 웹페이지와 SQL 쿼리를 실행하기 위한 텍스트 상자가 표시된다.

![page](/assets/img/2024-03-27-spring-boot-h2-database/page.png)

웹 콘솔에는 SQL 키워드를 제안하는 자동 완성 기능이 있다. 콘솔이 가볍기 때문에 데이터베이스를 시각적으로 검사하거나 원시 SQL을 직접 실행하는데 편리하다.

또한 프로젝트의 `application.properties`에 다음 속성을 원하는 값으로 지정하여 콘솔을 추가로 구성할 수 있다.

```properties
spring.h2.console.path=/h2-console
spring.h2.console.settings.trace=false
spring.h2.console.settings.web-allow-others=false
```

마찬가지로 YAML 구성을 사용할 때 위 속성을 다음과 같이 추가할 수 있다.

```yml
spring:
  h2:
    console:
      path: /h2-console
      settings.trace: false
      settings.web-allow-others: false
```

위의 코드 조각에서는 콘솔 경로를 실행 중인 애플리케이션의 주소 및 포트에 상대적인 `/h2-console`로 설정했다. 따라서 앱이 `http://localhost:9001`에서 실행 중인 경우 `http://localhost:9001/h2-console`에서 콘솔을 사용할 수 있다.

또한 추적 출력을 방지하기 위해 `spring.h2.console.settings.trace`를 false 로 설정하고 spring을 설정하여 원격 액세스를 비활성화할 수도 있다. `h2.console.settings.web-allow-others`를 false로 설정한다 .

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-h2-database](https://www.baeldung.com/spring-boot-h2-database)
