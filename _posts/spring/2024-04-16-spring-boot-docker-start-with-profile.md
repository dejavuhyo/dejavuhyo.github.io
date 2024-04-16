---
title: Spring Boot Profile로 Docker에서 애플리케이션 시작
author: dejavuhyo
date: 2024-04-16 10:50:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-docker, spring-docker, docker-profile]
---

## 1. 기본 Dockerfile
일반적으로 Spring Boot 애플리케이션을 고정화하기 위해 Dockerfile을 제공하기만 하면 된다.

Spring Boot 애플리케이션을 위한 최소 Dockerfile이다.

```text
FROM openjdk:17-jdk-alpine
COPY target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

물론 docker build를 통해 Docker 이미지를 빌드할 수 있다.

```shell
docker build --tag=docker-with-spring-profile:latest .
```

따라서 docker-with-spring-profile 이미지에서 애플리케이션을 실행할 수 있다.

```shell
docker run docker-with-spring-profile:latest
```

Spring Boot 애플리케이션은 "default"  프로필로 시작된다.

```text
2024-02-29 22:34:25.268 INFO 1 --- [main] c.b.docker.spring.DemoApplication: Starting DemoApplication using Java 17-ea with PID 1 (/app.jar started by root in /)
2024-02-29 22:34:25.270 INFO 1 --- [main] c.b.docker.spring.DemoApplication: No active profile set, falling back to 1 default profile: "default"
//...
```

## 2. Dockerfile에서 Dockerfile 설정
Docker화된 애플리케이션에 대한 프로필을 설정하는 한 가지 방법은 Spring Boot의 명령줄 인수 `-Dspring.profiles.active`를 사용하는 것이다.

따라서 프로필을 "test"로 설정하려면 Dockerfile의 ENTRYPOINT 줄에 `-Dspring.profiles.active=test`라는 새 인수를 추가한다.

```text
//...
ENTRYPOINT ["java", "-Dspring.profiles.active=test", "-jar", "/app.jar"]
```

프로필 변경 사항을 보려면 동일한 명령을 사용하여 컨테이너를 다시 실행한다.

```shell
docker run docker-with-spring-profile:latest
```

따라서 애플리케이션에서 "test" 프로필이 성공적으로 선택되었음을 확인할 수 있다.

```text
2024-02-29 22:39:33.210 INFO 1 --- [main] c.b.docker.spring.DemoApplication: Starting DemoApplication using Java 17-ea with PID 1 (/app.jar started by root in /)
2024-02-29 22:39:33.212 INFO 1 --- [main] c.b.docker.spring.DemoApplication: The following 1 profile is active: "test"
//...
```

## 3. 환경 변수를 사용하여 Profile 설정
때로는 Dockerfile에 하드 코딩된 프로필을 사용하는 것이 편리하지 않은 경우도 있다. 둘 이상의 프로필이 필요한 경우 컨테이너를 실행할 때 프로필 중 하나를 선택하는 것이 번거로울 수 있다.

그럼에도 불구하고 더 나은 대안이 있다. 시작하는 동안 Spring Boot는 특수 환경 변수 `SPRING_PROFILES_ACTIVE`를 찾는다.

따라서 `docker run` 명령과 함께 이를 실제로 활용하여 시작 시 Spring 프로필을 설정할 수 있다.

```shell
docker run -e "SPRING_PROFILES_ACTIVE=test" docker-with-spring-profile:latest
```

또한 사용 사례에 따라 쉼표로 구분된 문자열을 통해 한 번에 두 개 이상의 프로필을 설정할 수 있다.

```shell
docker run -e "SPRING_PROFILES_ACTIVE=test1,test2,test3" docker-with-spring-profile:latest
```

그러나 Spring Boot에는 속성 간에 특정 순서가 있다. 명령줄 인수는 환경 변수보다 우선한다. 이러한 이유로 `SPRING_PROFILES_ACTIVE`를 작동하려면 Dockerfile을 되돌려야 한다.

결과적으로 Dockerfile의 ENTRYPOINT 줄에서 "-Dspring.profiles.active=test" 인수를 제거한다.

```text
//...
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

마지막으로 `SPRING_PROFILES_ACTIVE`를 통해 설정한 프로필이 고려되는 것을 확인할 수 있다.

```text
2024-02-29 22:50:28.924 INFO 1 --- [main] c.b.docker.spring.DemoApplication: Starting DemoApplication using Java 17-ea with PID 1 (/app.jar started by root in /)
2024-02-29T22:50:28.926562249Z 2022-04-22 22:50:28.926 INFO 1 --- [main] c.b.docker.spring.DemoApplication: The following 3 profiles are active: "test1", "test2", "test3"
//..
```

## 4. Docker Compose 파일에서 Profile 설정
대체 접근 방식으로 환경 변수를 docker-compose 파일 에 제공할 수도 있습니다 .

또한 docker 실행 작업을 더 나은 방식으로 활용하기 위해 각 프로필에 대해 [docker-compose](https://www.baeldung.com/ops/docker-compose) 파일을 만들 수 있다.

"test" profile에 대한 `docker-compose-test.yml` 파일을 만들어 본다.

```yml
version: "3.5"
services:
  docker-with-spring-profile:
    image: docker-with-spring-profile:latest
    environment:
      - "SPRING_PROFILES_ACTIVE=test"
```

마찬가지로 "prod" profile에 대해 다른 파일 `docker-compose-prod.yml`을 만든다. 유일한 차이점은 두 번째 파일의 "prod" 프로필이다.

```yml
//...
environment:
  - "SPRING_PROFILES_ACTIVE=prod"
```

따라서 두 가지 다른 docker-compose 파일을 통해 컨테이너를 실행할 수 있다.

```shell
# for the profile 'test'
docker-compose -f docker-compose-test.yml up

# for the profile 'prod'
docker-compose -f docker-compose-prod.yml up
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-docker-start-with-profile](https://www.baeldung.com/spring-boot-docker-start-with-profile)
