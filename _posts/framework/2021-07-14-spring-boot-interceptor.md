---
title: Spring Boot 인터셉터 적용
author: dejavuhyo
date: 2021-07-14 05:00:00 +0900
categories: [Application, Framework]
tags: [spring-boot-interceptor, interceptor, 스프링부트-인터셉터, 인터셉터]
---

## 1. Interceptor란
인터셉터(Interceptor)는 스프링의 Spring Context(ApplicationContext) 기능으로 임의의 URI를 호출시 DispatcherServlet에서 해당 Controller가 처리되기 전과 후에 발생하는 이벤트이다.

![interceptor](/assets/img/2021-07-14-spring-boot-interceptor/interceptor.png)

## 2. 구현
HandlerInterceptorAdapterr를 상속받아 구현하며 preHandle, postHandle, afterCompletion, afterConcurrentHandlingStarted 네 개의 메소드를 포함하고 있다.

| 메소드 | 설명 |
|:-----:|:-----:|
| preHandle | 컨트롤러 실행 전 수행한다. 반환 값이 true일 경우 컨트롤러로 진입하고 false일 경우 진입하지 않는다. Object handler는 진입하려는 컨트롤러의 클래스 객체가 담겨있다. |
| postHandle | 컨트롤러 실행 후 View가 랜더링 되기 전에 수행한다. |
| afterCompletion | 컨트롤러 실행되고 view가 랜더링 된 후에 실행된다. |
| afterConcurrentHandlingStarted | 비동기 요청 시 PostHandle과 afterCompletion이 수행되지 않고 afterConcurrentHandlingStarted가 수행된다. |

* Interceptor 생성

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class HttpInterceptor implements HandlerInterceptor {

    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        logger.info("[preHandle]");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        logger.info("[postHandle]");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object object, Exception ex) throws Exception {
        logger.info("[afterCompletion]");
    }
}
```

* Interceptor 등록

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new HttpInterceptor())
                .addPathPatterns("/*")
                .excludePathPatterns("/board"); // 해당 경로는 인터셉터가 가로채지 않는다.
    }
}
```

* Controller 적용

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {

    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @GetMapping("/user")
    public String user() {
        logger.info("user");
        return "/user";
    }

    @GetMapping("/board")
    public String board() {
        logger.info("board");
        return "/board";
    }
}
```

## 3. 결과

* localhost:8080/users 호출

```text
[preHandle]
user
[postHandle]
[afterCompletion]
```

* localhost:8080/board 호출

```text
board
```

## [출처 및 참고]
* [https://linked2ev.github.io/gitlog/2019/09/15/springboot-mvc-12-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Interceptor-%EC%84%A4%EC%A0%95/](https://linked2ev.github.io/gitlog/2019/09/15/springboot-mvc-12-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Interceptor-%EC%84%A4%EC%A0%95/)
* [https://bamdule.tistory.com/149](https://bamdule.tistory.com/149)
