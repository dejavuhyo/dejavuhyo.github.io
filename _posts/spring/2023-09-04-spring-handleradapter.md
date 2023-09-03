---
title: Spring MVC HandlerAdapter
author: dejavuhyo
date: 2023-09-04 08:10:00 +0900
categories: [Framework, Spring]
tags: [spring-handleradapter, handleradapter]
---

## 1. Handleradapter란
HandlerAdapter는 기본적으로 Spring MVC에서 매우 유연한 방식으로 HTTP 요청 처리를 용이하게 하는 인터페이스이다.

이는 메소드를 특정 URL에 매핑하는 HandlerMapping과 함께 사용된다.

그러면 DispatcherServlet은 HandlerAdapter를 사용하여 이 메소드를 호출한다. 서블릿은 메소드를 직접 호출하지 않는다. 기본적으로 서블릿은 자신과 핸들러 객체 사이의 브리지 역할을 하여 느슨하게 결합된 디자인을 유도한다.

```java
public interface HandlerAdapter {
    boolean supports(Object handler);
    
    ModelAndView handle(
      HttpServletRequest request,
      HttpServletResponse response, 
      Object handler) throws Exception;
    
    long getLastModified(HttpServletRequest request, Object handler);
}
```

supports API는 특정 핸들러 인스턴스가 지원되는지 여부를 확인하는데 사용된다. 핸들러 인스턴스가 지원되는지 여부를 확인하려면 이 인터페이스의 `handler()` 메서드를 호출하기 전에 이 메서드를 먼저 호출해야 한다.

handle API는 특정 HTTP 요청을 처리하는데 사용된다. 이 메소드는 HttpServletRequest 및 HttpServletResponse 객체를 매개변수로 전달하여 핸들러를 호출하는 역할을 한다. 그런 다음 핸들러는 애플리케이션 로직을 실행하고 ModelAndView 객체를 반환한다. 이 객체는 DispatcherServlet에 의해 처리된다.

## 2. 메이븐 의존성
pom.xml 에 추가해야 하는 Maven 종속성이다.

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.8.RELEASE</version>
</dependency>
```

## 3. HandlerAdapter의 종류

### 1) SimpleControllerHandlerAdapter
Spring MVC에 등록된 기본 핸들러 어댑터이다. Controller 인터페이스를 구현하는 클래스를 다루고 컨트롤러 객체에 요청을 전달하는데 사용된다.

웹 애플리케이션이 컨트롤러만 사용하는 경우 프레임워크가 이 클래스를 요청 처리를 위한 기본 어댑터로 사용하므로 HandlerAdapter를 구성할 필요가 없다.

이전 스타일의 컨트롤러(Controller 인터페이스 구현)를 사용하여 간단한 컨트롤러 클래스를 정의한다.

```java
public class SimpleController implements Controller {
    @Override
    public ModelAndView handleRequest(
      HttpServletRequest request, 
      HttpServletResponse response) throws Exception {
        
        ModelAndView model = new ModelAndView("Greeting");
        model.addObject("message", "Dinesh Madhwal");
        return model;
    }
}
```

유사한 XML 구성이다.

```xml
<beans ...>
    <bean name="/greeting.html"
      class="com.baeldung.spring.controller.SimpleControllerHandlerAdapterExample"/>
    <bean id="viewResolver"
      class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

BeanNameUrlHandlerMapping 클래스는 이 핸들러 어댑터에 대한 매핑 클래스이다.

> 참고: 사용자 정의 핸들러 어댑터가 BeanFactory에 정의된 경우 이 어댑터는 자동으로 등록되지 않는다. 따라서 문맥에서 이를 명시적으로 정의해야 한다. 정의되지 않았고 사용자 정의 핸들러 어댑터를 정의한 경우 핸들러에 대한 어댑터가 지정되지 않았다는 예외가 발생한다.

### 2) SimpleServletHandlerAdapter
이 핸들러 어댑터를 사용 하면 요청 처리를 위해 DispatcherServlet과 함께 작동하는 서블릿을 사용할 수 있다. `service()` 메소드를 호출하여 DispatcherServlet의 요청을 적절한 Servlet 클래스로 전달한다.

Servlet 인터페이스를 구현하는 Bean은 이 어댑터에 의해 자동으로 처리된다. 이는 기본적으로 등록되지 않으며 DispatcherServlet의 구성 파일에 다른 일반 Bean처럼 등록해야 한다.

```xml
<bean name="simpleServletHandlerAdapter" class="org.springframework.web.servlet.handler.SimpleServletHandlerAdapter" />
```

### 3) AnnotationMethodHandlerAdapter
이 어댑터 클래스는 `@RequestMapping` 주석이 달린 메소드를 실행하는데 사용된다. HTTP 메서드와 HTTP 경로를 기반으로 메서드를 매핑하는데 사용된다.

이 어댑터의 매핑 클래스는 `@RequestMapping` 주석을 유형 수준에서 처리하는데 사용되는 DefaultAnnotationHandlerMapping 이고, AnnotationMethodHandlerAdaptor는 메서드 수준에서 처리하는데 사용된다.

이 두 클래스는 DispatcherServlet이 초기화될 때 프레임워크에 의해 이미 등록되어 있다. 그러나 다른 핸들러 어댑터가 이미 정의된 경우 구성 파일에서도 이를 정의해야 한다.

컨트롤러 클래스를 정의한다.

```java
@Controller
public class AnnotationHandler {
    @RequestMapping("/annotedName")
    public ModelAndView getEmployeeName() {
        ModelAndView model = new ModelAndView("Greeting");
        model.addObject("message", "Dinesh");
        return model;  
    }  
}
```

`@Controller` 주석은 이 클래스가 컨트롤러 역할을 한다는 것을 나타낸다.

`@RequestMapping` 주석은 `getEmployeeName()` 메서드를 URL `/name`에 매핑한다.

애플리케이션이 Java 기반 구성을 사용하는지 아니면 XML 기반 구성을 사용하는지에 따라 이 어댑터를 구성하는 방법에는 두 가지가 있다. Java 구성을 사용하는 첫 번째 방법이다.

```java
@ComponentScan("com.baeldung.spring.controller")
@Configuration
@EnableWebMvc
public class ApplicationConfiguration implements WebMvcConfigurer {
    @Bean
    public InternalResourceViewResolver jspViewResolver() {
        InternalResourceViewResolver bean = new InternalResourceViewResolver();
        bean.setPrefix("/WEB-INF/");
        bean.setSuffix(".jsp");
        return bean;
    }
}
```

애플리케이션이 XML 구성을 사용하는 경우 웹 애플리케이션 컨텍스트 XML에서 이 핸들러 어댑터를 구성하는 두 가지 접근 방식이 있다. `spring-servlet_AnnotationMethodHandlerAdapter.xml` 파일에 정의된 첫 번째 접근 방식이다.

```xml
<beans ...>
    <context:component-scan base-package="com.baeldung.spring.controller" />
    <bean 
      class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping"/>
    <bean 
      class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter"/>
    <bean id="viewResolver"
      class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

`<context:comComponent-scan />` 태그는 컨트롤러 클래스를 검색할 패키지를 지정하는데 사용된다.

두 번째 접근 방식이다.

```xml
<beans ...>
    <mvc:annotation-driven/>
    <context:component-scan base-package="com.baeldung.spring.controller" />
    <bean id="viewResolver"
      class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

`<mvc:annotation-driven>` 태그는 이 두 클래스를 spring MVC에 자동으로 등록한다. 이 어댑터는 Spring 3.2에서 더 이상 사용되지 않으며 RequestMappingHandlerAdapter라는 새로운 핸들러 어댑터가 Spring 3.1에 도입되었다.

### 4) RequestMappingHandlerAdapter
이 어댑터 클래스는 Spring 3.1에서 도입되었으며 Spring 3.2에서는 AnnotationMethodHandlerAdaptor 핸들러 어댑터를 더 이상 사용하지 않는다.

`@RequestMapping` 주석이 달린 메소드를 실행하는 RequestMappingHandlerMapping 클래스와 함께 사용된다.

RequestMappingHandlerMapping은 요청 URI와 핸들러의 매핑을 유지하는데 사용된다. 핸들러를 얻으면 DispatcherServlet은 해당 핸들러 어댑터에 요청을 전달하고 핸들러 어댑터는 `handlerMethod()`를 호출한다.

유형 수준 및 메서드 수준 매핑은 3.1 이전의 Spring 버전에서 두 가지 다른 단계로 처리된다.

첫 번째 단계는 DefaultAnnotationHandlerMapping으로 컨트롤러를 선택하는 것이고 두 번째 단계는 AnnotationMethodHandlerAdapter로 실제 메서드를 호출하는 것이다.

Spring 버전 3.1부터는 컨트롤러를 식별하고 요청을 처리하기 위해 호출해야 하는 메서드를 식별하는 단 하나의 단계만 있다.

간단한 컨트롤러 클래스를 정의한다.

```java
@Controller
public class RequestMappingHandler {
    
    @RequestMapping("/requestName")
    public ModelAndView getEmployeeName() {
        ModelAndView model = new ModelAndView("Greeting");
        model.addObject("message", "Madhwal");
        return model;
    }  
}
```

애플리케이션이 Java 기반 구성을 사용하는지 아니면 XML 기반 구성을 사용하는지에 따라 이 어댑터를 구성하는 방법에는 두 가지가 있다.

Java 구성을 사용하는 첫 번째 방법이다.

```java
@ComponentScan("com.baeldung.spring.controller")
@Configuration
@EnableWebMvc
public class ServletConfig implements WebMvcConfigurer {
    @Bean
    public InternalResourceViewResolver jspViewResolver() {
        InternalResourceViewResolver bean = new InternalResourceViewResolver();
        bean.setPrefix("/WEB-INF/");
        bean.setSuffix(".jsp");
        return bean;
    }
}
```

애플리케이션이 XML 구성을 사용하는 경우 웹 애플리케이션 컨텍스트 XML에서 이 핸들러 어댑터를 구성하는 두 가지 접근 방식이 있다. `spring-servlet_RequestMappingHandlerAdapter.xml` 파일에 정의된 첫 번째 접근 방식이다.

```xml
<beans ...>
    <context:component-scan base-package="com.baeldung.spring.controller" />
    
    <bean 
      class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
    
    <bean
      class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>
    
    <bean id="viewResolver"
      class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

두 번째 접근 방식은 다음과 같다.

```xml
<beans ...>
    <mvc:annotation-driven />
    
    <context:component-scan base-package="com.baeldung.spring.controller" />
    
    <bean id="viewResolver"
      class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/" />
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```

이 태그는 이 두 클래스를 Spring MVC에 자동으로 등록한다.

RequestMappingHandlerMapping을 사용자 정의해야 하는 경우 애플리케이션 컨텍스트 XML에서 이 태그를 제거하고 애플리케이션 컨텍스트 XML에서 수동으로 구성해야 한다.

### 5) HttpRequestHandlerAdapter
이 핸들러 어댑터는 HttpRequest를 처리하는 핸들러에 사용된다. 요청을 처리하고 응답을 생성하기 위한 단일 `handlerRequest()` 메서드가 포함된 HttpRequestHandler 인터페이스를 구현한다.

이 메서드의 반환 유형은 void이며 다른 핸들러 어댑터에서 생성된 ModelAndView 반환 유형을 생성하지 않는다. 기본적으로 이진 응답을 생성하는데 사용되며 렌더링할 뷰를 생성하지 않는다.

## 4. 애플리케이션 실행
애플리케이션이 포트 번호 8082를 사용하여 localhost에 배포되고 컨텍스트 루트가 spring-mvc-handlers인 경우이다.

```text
http://localhost:8082/spring-mvc-handlers/
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-handler-adapters](https://www.baeldung.com/spring-mvc-handler-adapters)
