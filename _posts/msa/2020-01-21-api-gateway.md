---
title: API Gateway
author: dejavuhyo
date: 2020-01-21 10:00:00 +0900
categories: [DevOps, MSA]
tags: [api-gateway, zuul, zuul-filter, prefilter, routefilter, postfilter, errorfilter, netflix-zuul]
---

## 1. API Gateway이란?
Microservice Architecture(MSA)에서 언급되는 컴포넌트 중 하나이며, 모든 클라이언트 요청에 대한 end point를 통합하는 서버이다. 마치 프록시 서버처럼 동작한다. 그리고 인증 및 권한, 모니터링, logging 등 추가적인 기능이 있다.

모든 비지니스 로직이 하나의 서버에 존재하는 Monolithic Architecture와 달리 MSA는 도메인별 데이터를 저장하고 도메인별로 하나 이상의 서버가 따로 존재한다. 한 서비스에 한개 이상의 서버가 존재하기 때문에 이 서비스를 사용하는 클라이언트 입장에서는 다수의 end point가 생기게 되며, end point를 변경이 일어났을때, 관리하기가 힘들다. 그래서 MSA 환경에서 서비스에 대한 도메인인 하나로 통합할 수 있는 API Gateway가 필요한 것이다.

## 2. Netflix Zuul이란?
Zuul is the front door for all requests from devices and web sites to the backend of the Netflix streaming application. As an edge service application, Zuul is built to enable dynamic routing, monitoring, resiliency and security. It also has the ability to route requests to multiple Amazon Auto Scaling Groups as appropriate.

즉, Netflix에서 사용하고 있는 API Gateway이다.

![zuul-netflix-cloud-architecture](/assets/img/2020-01-21-api-gateway/zuul-netflix-cloud-architecture.png)

## 3. Netflix에서 Zuul을 사용하는 이유?
Netflix API의 요청은 많은 트래픽과 다양한 형태의 요청으로 경고없이 문영에 문제를 발생시킨다. 이런한 상황에 신속히 대응할 수 있는 시스템 Zuul을 개발 하였다.

Zuul은 다양한 유형의 Filter를 사용하여 이러한 문제를 신속하고 동적으로 해결한다. Filter에 기능을 정의하고, 이슈사항 발생시 적절한 Filter를 추가함으로써 문제에 대비할 수 있다.

## 4. Netflix Filter의 기능

### 1) Authentication and Security
클라이언트 요청시, 각 리소스에 대한 인증 요구 사항을 식별하고 이를 만족시키지 않는 요청은 거부

### 2) Insights and Monitoring
의미있는 데이터 및 통계 제공

### 3) Dynamic Routing
필요에 따라 요청을 다른 클러스터로 동적으로 라우팅

### 4) Stress Testing
성능 측정을 위해 점차적으로 클러스터 트래픽을 증가

### 5) Load Shedding
각 유형의 요청에 대해 용량을 할당하고, 초과하는 요청은 제한

### 6) Static Response handling
클러스터에서 오는 응답을 대신하여 API Gateway에서 응답 처리

## 5. Zuul Filter
Zuul Filter는 4가지 Filter로 나누어 진다.

### 1) PRE Filter
라우팅전에 실행되는 필터로, 주로 인증, origin 서버 선택, 디버그 정보 logging을 한다.

### 2) ROUTING Filter
요청에 대한 라우팅을 처리하는 필터로, Apache HttpClient나 Netflix Ribbon을 사용하여 http 요청을 built하고 sent 한다.

### 3) POST Filter
라우팅 후에 실행되는 필터로, response에 대한 표준 HTTP header를 추가, 통계 및 metrics 수집, 요청에 대한 streaming을 한다.

### 4) ERROR Filter
오류가 발생하면 실행 된다.

![zuul-netflix-cloud-architecture](/assets/img/2020-01-21-api-gateway/request-lifecycle.png)

## 6. Zuul Filter 설정

### 1) Dependency 설정 (Maven)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
```

### 2) Application.java

```java
import com.example.zuul.filter.ErrorFilter;
import com.example.zuul.filter.PostFilter;
import com.example.zuul.filter.PreFilter;
import com.example.zuul.filter.RouteFilter;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;
import org.springframework.context.annotation.Bean;

@EnableZuulProxy
@SpringBootApplication
public class ZuulApplication {

   public static void main(String[] args) {
      SpringApplication.run(ZuulApplication.class, args);
   }

   @Bean
   public PreFilter preFilter() {
      return new PreFilter();
   }

   @Bean
   public PostFilter postFilter() {
      return new PostFilter();
   }

   @Bean
   public ErrorFilter errorFilter() {
      return new ErrorFilter();
   }

   @Bean
   public RouteFilter routeFilter() {
      return new RouteFilter();
   }
}
```

### 3) PreFilter.java

```java
package com.example.zuul.filter;

import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.servlet.http.HttpServletRequest;

public class PreFilter extends ZuulFilter {

    private final Logger log = LoggerFactory.getLogger(getClass());

    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        HttpServletRequest request = ctx.getRequest();

        log.info("addr : " + request.getRemoteAddr());
        log.info("method : " + request.getMethod());
        log.info("url : " + request.getRequestURL().toString());

        return null;
    }
}
```

### 4) RouteFilter.java

```java
package com.example.zuul.filter;

import com.netflix.zuul.ZuulFilter;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class RouteFilter extends ZuulFilter {

    private final Logger log = LoggerFactory.getLogger(getClass());

    @Override
    public String filterType() {
        return "route";
    }

    @Override
    public int filterOrder() {
        return 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() {
        log.info("Using Route Filter");

        return null;
    }
}
```

### 5) PostFilter.java

```java
package com.example.zuul.filter;

import com.netflix.zuul.ZuulFilter;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class PostFilter extends ZuulFilter {

    private final Logger log = LoggerFactory.getLogger(getClass());

    @Override
    public String filterType() {
        return "post";
    }

    @Override
    public int filterOrder() {
        return 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() {
        log.info("Using Post Filter");

        return null;
    }
}
```

### 6) ErrorFilter.java

```java
package com.example.zuul.filter;

import com.netflix.zuul.ZuulFilter;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class ErrorFilter extends ZuulFilter {

    private final Logger log = LoggerFactory.getLogger(getClass());

    @Override
    public String filterType() {
        return "error";
    }

    @Override
    public int filterOrder() {
        return 1;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() {
        log.info("Using Route Filter");

        return null;
    }
}
```

### 7) application.properties

```properties
# 포트
server.port=9999
# 어플리케이션 이름
spring.application.name=zuul

endpoints.restart.enabled=true
endpoints.shutdown.enabled=true
endpoints.health.sensitive=false

# 예외 처리
zuul.ignored-services='*'
zuul.ignored-patterns=/**/api/**

management.security.enabled=false
management.endpoints.web.exposure.include=*

#eureka.client.serviceUrl.defaultZone=http://localhost:9988/eureka/

############################################################
################### Route Configuration ####################
# Increase the Hystrix timeout to 6.5s (globally)
# Ribbon global settings
ribbon.retryable=true
# Max number of retries on the same server (excluding the first try)
ribbon.MaxAutoRetries=0
# Max number of next servers to retry (excluding the first server)
ribbon.MaxAutoRetriesNextServer=1
# Whether all operations can be retried for this client
ribbon.OkToRetryOnAllOperations=true
# Connect timeout used by Apache HttpClient
ribbon.ConnectTimeout=30000
# Read timeout used by Apache HttpClient
ribbon.ReadTimeout=30000
```

## [출처 및 참고]
* [https://github.com/Netflix/zuul/wiki](https://github.com/Netflix/zuul/wiki)
* [https://netflixtechblog.com/announcing-zuul-edge-service-in-the-cloud-ab3af5be08ee](https://netflixtechblog.com/announcing-zuul-edge-service-in-the-cloud-ab3af5be08ee)
* [http://woowabros.github.io/r&d/2017/06/13/apigateway.html](http://woowabros.github.io/r&d/2017/06/13/apigateway.html)
* [https://eblo.tistory.com/69](https://eblo.tistory.com/69](https://eblo.tistory.com/69](https://eblo.tistory.com/69)
