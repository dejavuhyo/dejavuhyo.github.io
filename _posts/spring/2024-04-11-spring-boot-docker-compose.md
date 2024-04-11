---
title: Spring Boot 3 Docker Compose 지원
author: dejavuhyo
date: 2024-04-11 10:53:00 +0900
categories: [Framework, Spring]
tags: [spring-docker-compose, docker-compose, spring-boot-docker]
---

## 1. Spring Boot 3 Docker Compose 지원
일반적으로 docker-compose up을 실행하여 시작하고 docker-compose down을 실행하여 `docker-compose.yml`을 기반으로 컨테이너를 중지한다. 이제 Docker Compose 명령을 Spring Boot 3에 위임할 수 있다. Spring Boot 애플리케이션이 시작되거나 중지되는 동안 컨테이너도 관리한다.

또한 SQL 데이터베이스, MongoDB, Cassandra 등과 같은 여러 서비스에 대한 관리 기능이 내장되어 있다. 따라서 애플리케이션 리소스 파일에 복제할 구성 클래스나 속성이 필요하지 않을 수도 있다.

마지막으로 사용자 지정 Docker 이미지 및 Docker Compose 프로필과 함께 이 지원을 사용할 수 있다.

## 2. 설정
새로운 지원을 살펴보려면 Docker Compose와 Spring Boot 3가 필요하다.

### 1) Docker Compose
[Docker Compose](https://docs.docker.com/compose/install/)를 사용하려면 [Docker](https://docs.docker.com/engine/install/) 엔진이 이미 설치되어 있어야 한다. OS에 따라 차이가 있을 수 있지만 설치가 쉽다.

Docker는 호스트에서 서비스로 실행된다. Docker 이미지를 통해 시스템에서 컨테이너를 경량 프로세스로 실행할 수 있다. 최소한의 Linux 커널 위에 여러 이미지의 레이어로 이미지를 볼 수 있다.

### 2) Spring Boot 3
Spring Boot 3 프로젝트를 설정하는 방법에는 몇 가지가 있다. 예를 들어 버전 3.1.0의 [Spring initializer](https://start.spring.io/) 프로그램을 사용할 수 있다. 그러나 프로젝트에 포함하는 종속성을 위해서는 항상 Spring Boot 3 스타터 라이브러리가 필요한다.

시작하려면 [parent](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent) POM을 추가한다.

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <relativePath />
</parent>
```

애플리케이션에 REST 엔드포인트를 사용하고 싶기 때문에 웹 종속성이 필요한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

예제 데이터베이스에 연결한다. 기본적으로 여러 가지 지원이 있다. MongoDB를 사용한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

애플리케이션이 실행 중인지 확인하기 위해 Spring Boot Actuator를 사용하여 확인한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

마지막으로 Docker Compose 종속성을 추가한다. 다른 프로젝트 기능을 사용하고 Docker Compose 지원을 제외하려면 true로 설정된 선택적 태그를 추가할 수 있다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-docker-compose</artifactId>
    <version>3.1.1</version>
</dependency>
```

Gradle을 사용하는 경우 BOM과 유사한 종속성 관리를 위해 [Spring Boot Gradle 플러그인](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/)을 살펴볼 수 있다.

## 3. Docker Compose 애플리케이션 킥스타트
MongoDB 데이터베이스를 사용하여 Spring Boot 3 애플리케이션을 만든다. 시작 시 spring-boot-docker-compose 종속성이 있으면 애플리케이션은 `docker-compose.yml` 파일의 모든 서비스를 실행한다.

### 1) Docker Compose File
먼저 docker-compose.yml 파일을 생성한다.

```yml
version: '3.8'
services:
  db:
    image: mongo:latest
    ports:
      - '27017:27017'
    volumes:
      - db:/data/db
volumes:
  db:
    driver:
      local
```

### 2) Spring Profile
Spring Boot 3에 Docker Compose 파일의 이름과 경로를 알려주어야 한다. 이를 `application-{profile} properties` 속성 또는 `YAML` 파일에 추가할 수 있다. docker-compose Spring 프로필을 사용한다. 따라서 `application-docker-compose.yml` 구성 파일을 생성한다.

```yml
spring:
  docker:
    compose:
      enabled: true
      file: docker-compose.yml
```

### 3) Database Configuration
데이터베이스 구성이 필요하지 않다. Docker Compose 지원은 기본 항목을 생성한다. 그러나 프로필을 사용하여 [MongoDB configuration](https://www.baeldung.com/spring-data-mongodb-tutorial)을 추가할 수 있다. 예를 들면 다음과 같다.

```java
@Profile("!docker-compose")
```

이렇게 하면 Docker Compose 지원을 사용할지 여부를 선택할 수 있다. 프로필을 사용하지 않으면 애플리케이션은 이미 실행 중인 데이터베이스를 예상한다.

### 4) Model
그런 다음 일반 항목에 대한 간단한 Document 클래스를 만든다.

```java
@Document("item")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Item {

    @Id
    private String id;
    private String name;
    private int quantity;
    private String category;
}
```

### 5) REST Controller
마지막으로 몇 가지 CRUD 작업을 사용하여 컨트롤러를 정의한다.

```java
@RestController
@RequestMapping("/item")
@RequiredArgsConstructor
public class ItemController {
    ....
    @PostMapping(consumes = APPLICATION_JSON_VALUE)
    public ResponseEntity<Item> save(final @RequestBody Item item) {
        return ResponseEntity.ok(itemRepository.save(item));
    }
    // other endpoints
}
```

## 4. Application Test
선호하는 IDE나 명령줄에서 기본 Spring Boot 3 클래스를 실행하여 애플리케이션을 시작할 수 있다.

### 1) Application Start
Spring 프로필을 언급하는 것을 기억한다. 예를 들어 명령줄에서 [Spring Boot maven plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/htmlsingle/)을 사용할 수 있다.

```shell
mvn spring-boot:run -Pdocker-compose -Dspring-boot.run.profiles=docker-compose
```

또한 다른 프로필이 있는 경우를 대비해 전용 Maven 빌드 프로필(-Pdocker-compose)을 추가하고 있다.

이제 docker ps 를 실행하면 MongoDb 컨테이너가 실행되는 것을 볼 수 있다.

```text
CONTAINER ID   IMAGE             COMMAND                  CREATED        STATUS            PORTS                                           NAMES
77a9667b291a   mongo:latest      "docker-entrypoint.s…"   21 hours ago   Up 10 minutes     0.0.0.0:27017->27017/tcp, :::27017->27017/tcp   classes-db-1
```

이제 애플리케이션으로 몇 가지 실시간 테스트를 수행할 수 있다.

### 2) Application Check
Actuator 엔드포인트를 사용하여 애플리케이션이 실행되고 있는지 확인할 수 있다.

```shell
curl --location 'http://localhost:8080/actuator/health'
```

모든 것이 괜찮다면 200 상태를 얻어야 한다.

```json
{
    "status": "UP"
}
```

데이터베이스 확인을 위해 엔드포인트 `http://localhost:8080/item`에서 POST 호출을 사용하여 일부 항목을 추가한다. 예를 들어, curl Post 요청을 확인한다.

```shell
curl --location 'http://localhost:8080/item' \
--header 'Content-Type: application/json' \
--data '{
    "name" : "Tennis Ball",
    "quantity" : 5,
    "category" : "sport"
}'
```

생성된 항목 ID로 응답을 받게 된다.

```json
{
    "id": "64b117b6a805f7296d8412d9",
    "name": "Tennis Ball",
    "quantity": 5,
    "category": "sport"
}
```

### 3) Application Shutdown
마지막으로 Spring Boot 3 애플리케이션을 종료하면 컨테이너도 중지된다. `docker ps -a` 를 실행하면 이를 확인할 수 있다.

```text
CONTAINER ID   IMAGE             COMMAND                  CREATED        STATUS                     PORTS     NAMES
77a9667b291a   mongo:latest      "docker-entrypoint.s…"   22 hours ago   Exited (0) 5 seconds ago             classes-db-1
```

## 5. Docker Compose 지원 기능
가장 관련성이 높은 [Docker Compose 지원 기능](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#features.docker-compose)이다.

### 1) 서비스 연결
이 지원이 시작 시 자동으로 검색되는 여러 서비스가 있다. MongoDB를 보았다. 그러나 [Redis](https://www.baeldung.com/spring-data-redis-tutorial) 또는 [ElasticSearch](https://www.baeldung.com/spring-data-elasticsearch-tutorial)와 같은 다른 것들도 있다. 서비스 연결은 로컬로 매핑된 포트를 찾아 사용한다. 구성 클래스나 속성을 건너뛸 수 있다. 이는 ConnectionDetails 추상화를 사용하여 Spring Boot에 의해 수행된다.

### 2) Custom Images
라벨을 적용하여 사용자 정의 Docker 이미지를 사용할 수 있다.

```yml
version: '3.8'
services:
  db:
    image: our-custom-mongo-image
    ports:
      - '27017:27017'
    volumes:
      - db:/data/db
    labels:
      org.springframework.boot.service-connection: mongo
volumes:
  db:
    driver:
      local
```

### 3) Container Readiness 기다리는 중
흥미롭게도 Spring Boot 3은 컨테이너 준비 상태를 자동으로 확인한다. 컨테이너가 완전히 준비되는데 시간이 걸릴 수 있다. 따라서 이 기능을 사용하면 [healthcheck](https://docs.docker.com/compose/compose-file/compose-file-v3/#healthcheck) 명령을 사용하여 컨테이너가 준비되었는지 확인할 수 있다.

### 4) Docker Compose 프로필 활성화
런타임 시 다양한 Docker Compose [profiles](https://docs.docker.com/compose/profiles/) 간에 전환할 수 있다. 서비스 정의는 복잡할 수 있으므로 예를 들어 디버그 또는 프로덕션 환경에 있는 경우 활성화할 프로필을 선택해야 할 수 있다. 구성 속성을 사용하여 이를 달성할 수 있다.

```properties
spring.docker.compose.profiles.active=myprofile
```

## 6. Docker Compose 지원의 이점
프로덕션 환경에서 Docker 서비스는 다양한 인스턴스에 분산될 수 있다. 따라서 이 경우에는 이 지원이 필요하지 않을 수도 있다. 그러나 로컬 개발에 대해서만 `docker-compose.yml` 정의에서 로드되는 Spring 프로필을 활성화할 수 있다.

이 지원은 IDE와 원활하게 통합되며 Docker 서비스를 시작하고 중지하기 위해 명령줄에서 앞뒤로 이동하지 않는다.

지원은 버전 3.1부터 시작되었다. 전반적으로 다중 서비스 연결, 서비스 준비 상태에 대한 기본 확인, Docker Compose profiles 사용 가능성과 같은 좋은 기능이 이미 있다.

## [출처 및 참고]
* [https://www.baeldung.com/docker-compose-support-spring-boot](https://www.baeldung.com/docker-compose-support-spring-boot)
