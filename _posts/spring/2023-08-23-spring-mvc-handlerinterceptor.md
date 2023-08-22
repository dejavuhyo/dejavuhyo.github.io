---
title: Spring MVC HandlerInterceptor
author: dejavuhyo
date: 2023-08-23 07:10:00 +0900
categories: [Framework, Spring]
tags: [spring-handlerinterceptor, handlerinterceptor, prehandle, posthandle, aftercompletion]
---

## 1. 스프링 MVC 핸들러
Spring 인터셉터가 작동하는 방식을 이해하기 위한 HandlerMapping을 확인한다.

HandlerMapping의 목적은 핸들러 메서드를 URL에 매핑하는 것이다. 그렇게 하면 DispatcherServlet이 요청을 처리할 때 호출할 수 있다.

실제로 DispatcherServlet은 HandlerAdapter를 사용하여 실제로 메서드를 호출한다.

요컨대 인터셉터는 요청을 가로채서 처리한다. 로깅 및 인증 확인과 같은 반복적인 핸들러 코드를 피하는데 도움이 된다.

HandlerInterceptor를 사용하여 전처리 및 후처리 작업을 수행하는 방법이다.

## 2. 메이븐 종속성
인터셉터를 사용하려면 `pom.xml`에 `spring-web` 종속성을 포함한다.

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.3.13</version>
</dependency>
```

## 3. 스프링 핸들러 인터셉터
Spring 인터셉터는 HandlerInterceptorAdapter 클래스를 확장하거나 HandlerInterceptor 인터페이스를 구현하는 클래스이다.

HandlerInterceptor에는 세 가지 주요 메서드가 포함되어 있다.

* `prehandle()` - 실제 핸들러 실행 전에 호출

* `postHandle()` - 핸들러가 실행된 후 호출

* `afterCompletion()` - 완료 요청이 완료되고 뷰가 생성된 후 호출

이 세 가지 방법은 모든 종류의 사전 및 사후 처리를 수행할 수 있는 유연성을 제공한다.

다음은 간단한 `preHandle()` 구현이다.

```java
@Override
public boolean preHandle(
  HttpServletRequest request,
  HttpServletResponse response, 
  Object handler) throws Exception {
    // your code
    return true;
}
```

이 메서드는 boolean 값을 반환한다. 요청을 추가로 처리(true)하거나 처리하지 않도록(false) Spring에 지시한다.

다음으로 `postHandle()` 구현이 있다.

```java
@Override
public void postHandle(
  HttpServletRequest request, 
  HttpServletResponse response,
  Object handler, 
  ModelAndView modelAndView) throws Exception {
    // your code
}
```

인터셉터는 요청을 처리한 직후 뷰를 생성하기 전에 이 메서드를 호출한다.

예를 들어 이 방법을 사용하여 로그인한 사용자의 아바타를 모델에 추가할 수 있다.

구현해야 하는 마지막 메서드는 `afterCompletion()` 이다.

```java
@Override
public void afterCompletion(
  HttpServletRequest request, 
  HttpServletResponse response,
  Object handler, Exception ex) {
    // your code
}
```

이 방법을 사용하면 요청 처리가 완료된 후 사용자 지정 논리를 실행할 수 있다.

또한 여러 사용자 지정 인터셉터를 등록할 수 있다. 이를 위해 `DefaultAnnotationHandlerMapping`을 사용할 수 있다.

## 4. 커스텀 로거 인터셉터
웹 애플리케이션 로그인에 중점을 둔다.

먼저 클래스는 HandlerInterceptor를 구현해야 한다.

```java
public class LoggerInterceptor implements HandlerInterceptor {
    ...
}
```

또한 인터셉터에서 로깅을 활성화해야 한다.

```java
private static Logger log = LoggerFactory.getLogger(LoggerInterceptor.class);
```

이를 통해 Log4J는 로그를 표시하고 현재 어떤 클래스가 지정된 출력에 정보를 로깅하고 있는지 표시할 수 있다.

다음으로 사용자 정의 인터셉터 구현이다.

### 1) preHandle() 메서드
이름에서 알 수 있듯이 인터셉터는 요청을 처리하기 전에 `preHandle()`을 호출한다.

기본적으로 이 메서드는 true를 반환하여 요청을 처리기 메서드에 추가로 보낸다. 그러나 false를 반환하여 실행을 중지하도록 Spring에 알릴 수 있다.

후크를 사용하여 요청의 출처와 같은 요청 매개변수에 대한 정보를 기록할 수 있다.

이 예에서는 간단한 Log4J 로거를 사용하여 이 정보를 기록한다.

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    log.info("[preHandle][" + request + "]" + "[" + request.getMethod() + "]" + request.getRequestURI() + getParameters(request));
    return true;
}
```

요청에 대한 몇 가지 기본 정보를 기록하고 있다.

여기에서 비밀번호를 발견할 경우 물론 비밀번호를 기록하지 않도록 해야 한다. 간단한 옵션은 암호 및 기타 민감한 유형의 데이터를 별표로 바꾸는 것이다.

이를 수행하는 방법에 대한 빠른 구현은 다음과 같다.

```java
private String getParameters(HttpServletRequest request) {
    StringBuffer posted = new StringBuffer();
    Enumeration<?> e = request.getParameterNames();
    if (e != null) {
        posted.append("?");
    }
    while (e.hasMoreElements()) {
        if (posted.length() > 1) {
            posted.append("&");
        }
        String curr = (String) e.nextElement();
        posted.append(curr + "=");
        if (curr.contains("password") 
          || curr.contains("pass")
          || curr.contains("pwd")) {
            posted.append("*****");
        } else {
            posted.append(request.getParameter(curr));
        }
    }
    String ip = request.getHeader("X-FORWARDED-FOR");
    String ipAddr = (ip == null) ? getRemoteAddr(request) : ip;
    if (ipAddr!=null && !ipAddr.equals("")) {
        posted.append("&_psip=" + ipAddr); 
    }
    return posted.toString();
}
```

마지막으로 HTTP 요청의 소스 IP 주소를 얻는 것이다.

다음은 간단한 구현이다.

```java
private String getRemoteAddr(HttpServletRequest request) {
    String ipFromHeader = request.getHeader("X-FORWARDED-FOR");
    if (ipFromHeader != null && ipFromHeader.length() > 0) {
        log.debug("ip from proxy - X-FORWARDED-FOR : " + ipFromHeader);
        return ipFromHeader;
    }
    return request.getRemoteAddr();
}
```

### 2) postHandle() 메서드
인터셉터는 핸들러 실행 후 DispatcherServlet이 보기를 렌더링하기 전에 이 메소드를 호출한다.

이를 사용하여 ModelAndView에 속성을 추가할 수 있다. 또 다른 사용 사례는 요청 처리 시간을 계산하는 것이다.

아래는 DispatcherServlet이 보기를 렌더링하기 직전에 요청을 간단히 기록한다.

```java
@Override
public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
    log.info("[postHandle][" + request + "]");
}
```

### 3) afterCompletion() 메서드
뷰가 렌더링된 후 이 메서드를 사용하여 요청 및 응답 데이터를 얻을 수 있다.

```java
@Override
public void afterCompletion(
  HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) 
  throws Exception {
    if (ex != null){
        ex.printStackTrace();
    }
    log.info("[afterCompletion][" + request + "][exception: " + ex + "]");
}
```

## 5. 구성
사용자 정의 인터셉터를 추가한다.

그렇게 하려면 `addInterceptors()` 메서드를 재정의해야 한다.

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new LoggerInterceptor());
}
```

XML Spring 구성 파일을 편집하여 동일한 구성을 얻을 수 있다.

```xml
<mvc:interceptors>
    <bean id="loggerInterceptor" class="com.baeldung.web.interceptor.LoggerInterceptor"/>
</mvc:interceptors>
```

이 구성이 활성화되면 인터셉터가 활성화되고 애플리케이션의 모든 요청이 제대로 기록된다.

여러 개의 Spring 인터셉터가 구성된 경우 `preHandle()` 메소드는 구성 순서로 실행되는 반면 `postHandle()` 및 `afterCompletion()` 메소드는 역순으로 호출된다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-handlerinterceptor](https://www.baeldung.com/spring-mvc-handlerinterceptor)
