---
title: Spring Cloud Gateway 구성
author: dejavuhyo
date: 2021-12-31 09:30:00 +0900
categories: [DevOps, MSA]
tags: [spring-cloud-gateway-configuration, spring-cloud-gateway, gateway-configuration, api-gateway, gateway, scg, spring-cloud, 게이트웨이, api-게이트웨이, api-게이트웨이-구성, 게이트웨이-구성]
---

## 1. 프로젝트 생성

* New Project → Spring Initializer

![spring-initializer](/assets/img/2021-12-31-spring-cloud-gateway-configuration/spring-initializer.png)

## 2. Dependencies 추가

* Gateway

* Eureka Discovery Client

* Spring Boot Actuator

* Lombok

![added-dependencies](/assets/img/2021-12-31-spring-cloud-gateway-configuration/added-dependencies.png)

## 3. 설정

### 1) GatewayApplication.java 설정
`@EnableEurekaClient` 어노테이션을 추가한다.

```java
package com.example.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@EnableEurekaClient
@SpringBootApplication
public class GatewayApplication {

    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }

}
```

### 2) GlobalFilter.java 추가

```java
package com.example.gateway;

import lombok.Data;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.cloud.gateway.filter.GatewayFilter;
import org.springframework.cloud.gateway.filter.factory.AbstractGatewayFilterFactory;
import org.springframework.stereotype.Component;
import reactor.core.publisher.Mono;

@Component
public class GlobalFilter extends AbstractGatewayFilterFactory<GlobalFilter.Config> {
    private static final Logger logger = LogManager.getLogger(GlobalFilter.class);

    public GlobalFilter() {
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(Config config) {
        return ((exchange, chain) -> {
            logger.info("GlobalFilter baseMessage : " + config.getBaseMessage());
            if (config.isPreLogger()) {
                logger.info("GlobalFilter Start : " + exchange.getRequest());
            }
            return chain.filter(exchange).then(Mono.fromRunnable(() -> {
                if (config.isPostLogger()) {
                    logger.info("GlobalFilter End : " + exchange.getResponse());
                }
            }));
        });
    }

    @Data
    public static class Config {
        private String baseMessage;
        private boolean preLogger;
        private boolean postLogger;
    }
}
```

### 3) BoardFilter.java 추가
BoardFilter 사용을 위해 추가한다. GlobalFilter와 같다.

```java
package com.example.gateway;

import lombok.Data;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.cloud.gateway.filter.GatewayFilter;
import org.springframework.cloud.gateway.filter.factory.AbstractGatewayFilterFactory;
import org.springframework.stereotype.Component;
import reactor.core.publisher.Mono;

@Component
public class BoardFilter extends AbstractGatewayFilterFactory<BoardFilter.Config> {
    private static final Logger logger = LogManager.getLogger(BoardFilter.class);

    public BoardFilter() {
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(Config config) {
        return ((exchange, chain) -> {
            logger.info("BoardFilter baseMessage : " + config.getBaseMessage());
            if (config.isPreLogger()) {
                logger.info("BoardFilter Start : " + exchange.getRequest());
            }
            return chain.filter(exchange).then(Mono.fromRunnable(() -> {
                if (config.isPostLogger()) {
                    logger.info("BoardFilter End : " + exchange.getResponse());
                }
            }));
        });
    }

    @Data
    public static class Config {
        private String baseMessage;
        private boolean preLogger;
        private boolean postLogger;
    }
}
```

### 4) application.yml 설정

```yaml
server:
  port: 8080

eureka:
  client:
    fetch-registry: false
    register-with-eureka: false
    service-url:
      defaultZone: http://localhost:8761/eureka

# Gateway Route 노출 설정
management:
  endpoints:
    web:
      exposure:
        include:
          - "gateway"
  endpoint:
    gateway:
      enabled: true

spring:
  application:
    name: gateway-service
  cloud:
    gateway:
      default-filters:
        - name: GlobalFilter # GlobalFilter 설정
          args:
            baseMessage: Spring Cloud Gateway GlobalFilter
            preLogger: true
            postLogger: true
      routes:
        - id: board-service # board-rest-api 설정
          uri: http://localhost:8081/
          predicates:
            - Path=/board/**
          filters:
            - name: BoardFilter # BoardFilter 설정
              args:
                baseMessage: Spring Cloud Gateway BoardFilter
                preLogger: true
                postLogger: true
```

## 4. 실행
IntelliJ 기준으로 Eureka, Gateway, Board 모듈을 import 하고, 각 모듈을 실행한다.

* Eureka 포트: 8761

* Gateway 포트: 8080

* Board 포트: 8081

![modules](/assets/img/2021-12-31-spring-cloud-gateway-configuration/modules.png)

`http://localhost:8080/board` board 서비스를 호출한다.

![service](/assets/img/2021-12-31-spring-cloud-gateway-configuration/service.png)

## [출처 및 참고]
* [https://cloud.spring.io/spring-cloud-gateway/reference/html/](https://cloud.spring.io/spring-cloud-gateway/reference/html/)
* [https://twofootdog.tistory.com/64](https://twofootdog.tistory.com/64)
* [https://cheese10yun.github.io/spring-cloud-gateway/](https://cheese10yun.github.io/spring-cloud-gateway/)
* [https://happycloud-lee.tistory.com/218](https://happycloud-lee.tistory.com/218)
