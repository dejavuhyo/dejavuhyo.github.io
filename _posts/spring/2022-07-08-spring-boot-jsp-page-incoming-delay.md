---
title: Spring Boot JSP 페이지 수신 딜레이
author: dejavuhyo
date: 2022-07-08 10:30:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-jsp, spring-boot-jsp-delay, jsp-page-delay, jsp-incoming-delay, spring-boot-jsp-딜레이, spring-boot-jsp-수신-지연, jsp-페이지-딜레이, jsp-딜레이, jsp-수신-지연]
---

## 1. 증상
Spring Boot 2.6.6 + Tomcat 9 + JSP에서 첫 번째 웹 페이지 수신이 매우 느려지는 현상이다.

> 응용 프로그램은 WAR 파일로 압축되었다.

Spring Boot 자체에서도 JSP를 정식 지원하지 않다 보니 이와 같은 현상이 발생하는 경우가 많이 있다.

## 2. 해결방법
JSP 파일에 대한 사전 컴파일을 실행하는 로직을 추가하는 것으로 웹 페이지 수신이 느린 문제를 해결하였다.

```java
import org.apache.catalina.Context;
import org.apache.catalina.webresources.ExtractingRoot;
import org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory;
import org.springframework.boot.web.server.WebServerFactoryCustomizer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class TomcatConfig {

    @Bean
    public WebServerFactoryCustomizer<TomcatServletWebServerFactory> servletContainerCustomizer() {
        return (TomcatServletWebServerFactory container) -> {
            container.addContextCustomizers((Context context) -> {
                // This configuration is used to improve initialization performance.

                context.setResources(new ExtractingRoot());
                context.setReloadable(false);
            });
        };
    }
}
```

## [출처 및 참고]
* [https://inseok9068.github.io/springboot/springboot-jsp-delay/](https://inseok9068.github.io/springboot/springboot-jsp-delay/)
* [https://dirask.com/questions/Spring-Boot-2-4-Tomcat-9-is-serving-web-page-response-content-extremely-slow-for-first-API-requests-1XrwYp](https://dirask.com/questions/Spring-Boot-2-4-Tomcat-9-is-serving-web-page-response-content-extremely-slow-for-first-API-requests-1XrwYp)
