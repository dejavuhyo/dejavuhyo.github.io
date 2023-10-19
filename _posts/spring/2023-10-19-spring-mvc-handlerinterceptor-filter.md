---
title: Spring MVC HandlerInterceptors와 Filters
author: dejavuhyo
date: 2023-10-19 22:10:00 +0900
categories: [Framework, Spring]
tags: [spring-handlerinterceptor, spring-filter, handlerinterceptor-filter]
---

## 1. Filters
필터는 Spring 프레임워크가 아닌 웹서버의 일부이다. 들어오는 요청의 경우 필터를 사용하여 요청이 서블릿에 도달하지 못하도록 조작하고 차단할 수도 있다. 그 반대로 응답이 클라이언트에 도달하지 못하도록 차단할 수도 있다.

[Spring Security](https://www.baeldung.com/security-spring)는 인증 및 권한 부여에 필터를 사용하는 좋은 예이다. Spring Security를 ​​구성하려면 [DelegatingFilterProxy](https://www.baeldung.com/spring-delegating-filter-proxy)라는 단일 필터를 추가하기만 하면 된다. 그런 다음 Spring Security는 들어오고 나가는 모든 트래픽을 가로챌 수 있다. 이것이 Spring Security가 Spring MVC 외부에서 사용될 수 있는 이유이다.

### 1) 필터 만들기
필터를 생성하려면 먼저 `javax.servlet.Filter` 인터페이스를 구현하는 클래스를 생성한다.

```java
@Component
public class LogFilter implements Filter {

    private Logger logger = LoggerFactory.getLogger(LogFilter.class);

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 
      throws IOException, ServletException {
        logger.info("Hello from: " + request.getLocalAddr());
        chain.doFilter(request, response);
    }

}
```

다음으로 ServletRequest, ServletResponse 또는 FilterChain 개체에 액세스하거나 조작할 수 있는 doFilter 메서드를 재정의한다. FilterChain 개체를 사용하여 요청을 허용하거나 차단할 수 있다.

마지막으로 `@Component`로 주석을 달아 Spring 컨텍스트에 필터를 추가한다. 나머지는 Spring이 알아서 할 것이다.

## 2. HandlerInterceptors
[HandlerInterceptor](https://www.baeldung.com/spring-mvc-handlerinterceptor)는 Spring MVC 프레임워크의 일부이며 DispatcherServlet과 Controller 사이에 위치한다. 요청이 컨트롤러에 도달하기 전과 뷰가 렌더링되기 전과 후에 요청을 가로챌 수 있다.

### 1) HandlerInterceptor 생성
HandlerInterceptor를 생성하기 위해 `org.springframework.web.servlet.HandlerInterceptor` 인터페이스를 구현하는 클래스를 생성한다. 이는 세 가지 방법을 재정의할 수 있는 옵션을 제공한다.

* `preHandle()` - 대상 핸들러가 호출되기 전에 실행된다.

* `postHandle()` - 대상 핸들러 이후, DispatcherServlet이 뷰를 렌더링하기 전에 실행된다.

* `afterCompletion()` - 요청 처리 및 뷰 렌더링 완료 후 콜백된다.

테스트 인터셉터의 세 가지 메소드에 로깅을 추가한다.

```java
public class LogInterceptor implements HandlerInterceptor {

    private Logger logger = LoggerFactory.getLogger(LogInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) 
      throws Exception {
        logger.info("preHandle");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) 
      throws Exception {
        logger.info("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) 
      throws Exception {
        logger.info("afterCompletion");
    }

}
```

## 3. 주요 차이점 및 사용 사례
요청/응답 흐름에서 Filter와 HandlerInterceptor가 적합한 위치를 보여주는 다이어그램이다.

![filters-vs-interceptors](/assets/img/2023-10-19spring-mvc-handlerinterceptor-filter/filters-vs-interceptors.png)

필터는 요청이 DispatcherServlet에 도달하기 전에 가로채서 다음과 같은 대략적인 작업에 이상적이다.

* 입증

* 로깅 및 감사

* 이미지 및 데이터 압축

* Spring MVC에서 분리하려는 모든 기능

반면에 HandlerIntercepor은 DispatcherServlet과 Controller 사이의 요청을 가로챈다. 이는 Spring MVC 프레임워크 내에서 수행되어 Handler 및 ModelAndView 개체에 대한 액세스를 제공한다. 이렇게 하면 중복이 줄어들고 다음과 같은 더욱 세분화된 기능이 가능해진다.

* 애플리케이션 로깅과 같은 교차 문제 처리

* 자세한 승인 확인

* Spring 컨텍스트 또는 모델 조작

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-handlerinterceptor-vs-filter](https://www.baeldung.com/spring-mvc-handlerinterceptor-vs-filter)
