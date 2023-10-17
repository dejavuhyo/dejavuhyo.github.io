---
title: Spring MVC 핸들러 인터셉터를 사용하여 세션 관리
author: dejavuhyo
date: 2023-10-17 22:35:00 +0900
categories: [Framework, Spring]
tags: [spring-sessions, handler-interceptor, spring-interceptor, manage-sessions, interceptor, 스프링-세션, 핸들러-인터셉터]
---

## 1. 메이븐 의존성
Interceptors를 사용하려면 `pom.xml` 파일의 종속성 섹션에 다음 섹션을 포함해야 한다.

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.3.13</version>
</dependency>
```

이 종속성은 Spring Web에만 적용되므로 전체(최소) 웹 애플리케이션에 대해 spring-core 및 spring-context를 추가한다.

## 2. 세션 시간 초과의 사용자 정의 구현
예에서는 시스템 사용자의 최대 비활성 시간을 구성한다. 그 이후에는 애플리케이션에서 자동으로 로그아웃된다.

이 논리는 단지 개념 증명일 뿐이다. 물론 세션 시간 초과를 사용하여 동일한 결과를 쉽게 얻을 수 있다. 그러나 여기서는 결과가 중요한 것이 아니라 인터셉터의 사용법이 중요하다.

따라서 사용자가 활성 상태가 아닌 경우 세션이 무효화되도록 하고 싶다. 예를 들어, 사용자가 로그아웃하는 것을 잊어버린 경우 비활성 시간 카운터는 승인되지 않은 사용자가 계정에 액세스하는 것을 방지한다. 그렇게 하려면 비활성 시간에 대해 상수를 설정해야 한다.

```java
private static final long MAX_INACTIVE_SESSION_TIME = 5 * 10000;
```

테스트 목적으로 50초로 설정했다. ms 단위로 계산된다.

앱의 각 세션을 추적해야 하므로 다음 Spring 인터페이스를 포함해야 한다.

```java
@Autowired
private HttpSession session;
```

### 1) preHandle()
이 방법에는 다음 작업이 포함된다.

* 요청 처리 시간을 확인하기 위해 타이머 설정

* 사용자가 로그인되어 있는지 확인(UserInterceptor 메소드 사용)

* 사용자의 비활성 세션 시간이 최대 허용 값을 초과하면 자동 로그 아웃

구현 부분이다.

```java
@Override
public boolean preHandle(
  HttpServletRequest req, HttpServletResponse res, Object handler) throws Exception {
    log.info("Pre handle method - check handling start time");
    long startTime = System.currentTimeMillis();
    request.setAttribute("executionTime", startTime);
}
```

코드의 이 부분에서는 실행 처리의 startTime을 설정한다. 이 순간부터 각 요청 처리를 완료하는 데 걸리는 시간(초)을 계산한다. 다음 부분에서는 누군가 HTTP 세션 중에 로그인한 경우에만 세션 시간에 대한 논리를 제공한다.

```java
if (UserInterceptor.isUserLogged()) {
    session = request.getSession();
    log.info("Time since last request in this session: {} ms",
      System.currentTimeMillis() - request.getSession().getLastAccessedTime());
    if (System.currentTimeMillis() - session.getLastAccessedTime()
      > MAX_INACTIVE_SESSION_TIME) {
        log.warn("Logging out, due to inactive session");
        SecurityContextHolder.clearContext();
        request.logout();
        response.sendRedirect("/spring-rest-full/logout");
    }
}
return true;
```

먼저 요청에서 세션을 가져와야 한다.

다음으로, 사용자가 애플리케이션에서 작업을 수행한 이후 로그인한 사람과 경과된 시간에 대한 콘솔 로깅을 수행한다. `session.getLastAccessedTime()`을 사용하여 이 정보를 얻고 현재 시간에서 이를 뺀 다음 `MAX_INACTIVE_SESSION_TIME`과 비교할 수 있다.

시간이 허용하는 것보다 길면 컨텍스트를 지우고 요청을 로그아웃한 다음 (선택적으로) Spring Security 구성 파일에 선언된 기본 로그아웃 보기에 대한 응답으로 리디렉션을 보낸다.

처리 시간 예제에 대한 카운터를 완성하기 위해 아래에서 설명하는 `postHandle()` 메서드도 구현한다.

### 2) postHandle()
이 방법은 현재 요청을 처리하는데 걸린 시간에 대한 정보를 얻기 위한 구현이다. 이전 코드에서 본 것처럼 Spring 모델에서 실행 시간을 설정했다. 이제 그것을 사용할 시간이다.

```java
@Override
public void postHandle(
  HttpServletRequest request, 
  HttpServletResponse response,
  Object handler, 
  ModelAndView model) throws Exception {
    log.info("Post handle method - check execution time of handling");
    long startTime = (Long) request.getAttribute("executionTime");
    log.info("Execution time for handling the request was: {} ms",
      System.currentTimeMillis() - startTime);
}
```

구현은 간단하다. 실행 시간을 확인하고 이를 현재 시스템 시간에서 뺀다. 모델의 값을 `long`으로 변환한다.

이제 실행 시간을 제대로 기록할 수 있다.

## 3. 인터셉터 구성
새로 생성된 인터셉터를 Spring 구성에 추가하려면 `WebMvcConfigurer`를 구현하는 WebConfig 클래스 내에서 `addInterceptors()` 메서드를 재정의해야 한다.

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new SessionTimerInterceptor());
}
```

XML Spring 구성 파일을 편집하여 동일한 구성을 얻을 수 있다.

```xml
<mvc:interceptors>
    <bean id="sessionTimerInterceptor" class="com.baeldung.web.interceptor.SessionTimerInterceptor"/>
</mvc:interceptors>
```

또한 ApplicationContext 생성을 자동화하려면 리스너를 추가해야 한다.

```java
public class ListenerConfig implements WebApplicationInitializer {
    @Override
    public void onStartup(ServletContext sc) throws ServletException {
        sc.addListener(new RequestContextListener());
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-custom-handler-interceptor](https://www.baeldung.com/spring-mvc-custom-handler-interceptor)
