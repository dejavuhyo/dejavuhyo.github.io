---
title: Spring Boot에서 Redis Session Storage 사용
author: dejavuhyo
date: 2022-05-12 17:15:00 +0900
categories: [Framework, Spring]
tags: [spring-session-storage, redis, spring-boot-redis, spring-redis, spring-boot-redis-session, redis-session, spring-redis-session, spring-boot-session, 레디스-세션, 스프링-레디스-세션, 스프링-부트-레디스, 스프링-부트-레디스-세션]
---

## 1. Spring Boot에서 Redis 설정

### 1) dependency 추가

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
</dependency>
```

spring-boot-starter-data-redis는 spring에서 redis를 사용하기 위한 의존성 추가이며, spring-session-data-redis는 spring의 session storage로써 기존의 메모리가 아닌 redis를 이용해 세션 값들을 저장 하기 위한 의존성이다.

### 2) EnableRedisHttpSession 설정

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.session.data.redis.config.annotation.web.http.EnableRedisHttpSession;

@SpringBootApplication
@EnableRedisHttpSession
public class RedisSessionApplication {

    public static void main(String[] args) {
        SpringApplication.run(RedisSessionApplication.class, args);
    }

}
```

### 3) application.properties Redis 접속 설정

```properties
spring.redis.host=localhost
spring.redis.password=
spring.redis.port=6379
spring.redis.pool.max-active=8
spring.redis.pool.max-idle=8
spring.redis.pool.min-idle=0
spring.redis.pool.max-wait=-1
```

### 4) RedisConfig.java 설정

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        LettuceConnectionFactory lettuceConnectionFactory = new LettuceConnectionFactory();
        return lettuceConnectionFactory;
    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate() {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory());
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new StringRedisSerializer());
        return redisTemplate;
    }

    @Bean
    public StringRedisTemplate stringRedisTemplate() {
        StringRedisTemplate stringRedisTemplate = new StringRedisTemplate();
        stringRedisTemplate.setKeySerializer(new StringRedisSerializer());
        stringRedisTemplate.setValueSerializer(new StringRedisSerializer());
        stringRedisTemplate.setConnectionFactory(redisConnectionFactory());
        return stringRedisTemplate;
    }
}
```

## 2. 테스트

### 1) SessionController.java 생성

```java
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpSession;

@RestController
public class SessionController {

    @PostMapping("/login")
    public String login(HttpSession session) {

        // 세션 생성
        session.setMaxInactiveInterval(-1);
        session.setAttribute("id", "userid");
        session.setAttribute("name", "username");
        return session.getId();
    }

    @PostMapping("/logout")
    public String logout(HttpSession session) {

        // 세션 삭제
        session.invalidate();
        return session.getId();
    }

    @PostMapping("/info")
    public String info(HttpSession session) {

        // 세션값 가져오기
        Object id = session.getAttribute("id");
        Object name = session.getAttribute("name");

        return id.toString() + ", " + name.toString();
    }
}
```

### 2) 로그인

![login](/assets/img/2022-05-12-spring-boot-using-redis-session-storage/login.png)

### 3) 세션 정보

![info](/assets/img/2022-05-12-spring-boot-using-redis-session-storage/info.png)

### 4) 로그아웃

![logout](/assets/img/2022-05-12-spring-boot-using-redis-session-storage/logout.png)

### 5) 로그아웃 후 세션 정보

![info_error](/assets/img/2022-05-12-spring-boot-using-redis-session-storage/info_error.png)

## 3. 소스 코드

* [https://github.com/dejavuhyo/redis-session](https://github.com/dejavuhyo/redis-session)

## [출처 및 참고]
* [https://velog.io/@albaneo0724/Spring-Redis%EB%A5%BC-session-storage%EB%A1%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0](https://velog.io/@albaneo0724/Spring-Redis%EB%A5%BC-session-storage%EB%A1%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
