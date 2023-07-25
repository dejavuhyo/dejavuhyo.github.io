---
title: Spring MVC
author: dejavuhyo
date: 2023-07-26 08:20:00 +0900
categories: [Framework, Spring]
tags: [spring-mvc, 스프링-mvc]
---

## 1. 스프링 MVC란
이름에서 알 수 있듯이 `Model-View-Controller` 또는 MVC 패턴을 다루는 Spring 프레임워크의 모듈이다. MVC 패턴의 모든 장점과 Spring의 편리함을 결합한다.

Spring은 DispatcherServlet을 사용하여 전면 컨트롤러 패턴으로 MVC를 구현한다.

간단히 말해서 DispatcherServlet은 요청을 의도한 목적지로 라우팅하는 주 컨트롤러 역할을 한다. 모델은 애플리케이션의 데이터일 뿐이며 뷰는 다양한 템플릿 엔진으로 표현된다.

## 2. Java 구성을 사용하는 Spring MVC
Java 구성 클래스를 통해 Spring MVC 지원을 활성화하려면 `@EnableWebMvc` 주석을 추가하기만 하면 된다.

```java
@EnableWebMvc
@Configuration
public class WebConfig {

    /// ...
}
```

이렇게 하면 컨트롤러 및 매핑 등록, 형식 변환기, 유효성 검사 지원, 메시지 변환기 및 예외 처리와 같은 MVC 프로젝트에 필요한 기본 지원이 설정된다.

이 구성을 사용자 지정하려면 WebMvcConfigurer 인터페이스를 구현해야 한다.

```java
@EnableWebMvc
@Configuration
public class WebConfig implements WebMvcConfigurer {

   @Override
   public void addViewControllers(ViewControllerRegistry registry) {
      registry.addViewController("/").setViewName("index");
   }

   @Bean
   public ViewResolver viewResolver() {
      InternalResourceViewResolver bean = new InternalResourceViewResolver();

      bean.setViewClass(JstlView.class);
      bean.setPrefix("/WEB-INF/view/");
      bean.setSuffix(".jsp");

      return bean;
   }
}
```

이 예제에서는 `/WEB-INF/view` 디렉토리 에서 `.jsp` 뷰를 반환할 ViewResolver 빈을 등록했다.

`ViewControllerRegistry`를 사용하여 URL과 뷰 이름 사이에 직접 매핑을 생성하는 뷰 컨트롤러를 등록할 수 있다. 이렇게 하면 둘 사이에 컨트롤러가 필요하지 않다.

컨트롤러 클래스도 정의하고 스캔하려면 컨트롤러가 포함된 패키지에 `@ComponentScan` 주석을 추가할 수 있다.

```java
@EnableWebMvc
@Configuration
@ComponentScan(basePackages = { "com.baeldung.web.controller" })
public class WebConfig implements WebMvcConfigurer {
    // ...
}
```

이 구성을 로드하는 애플리케이션을 부트스트랩하려면 초기화 클래스도 필요하다.

```java
public class MainWebAppInitializer implements WebApplicationInitializer {
    @Override
    public void onStartup(final ServletContext sc) throws ServletException {

        AnnotationConfigWebApplicationContext root = 
          new AnnotationConfigWebApplicationContext();
        
        root.scan("com.baeldung");
        sc.addListener(new ContextLoaderListener(root));

        ServletRegistration.Dynamic appServlet = 
          sc.addServlet("mvc", new DispatcherServlet(new GenericWebApplicationContext()));
        appServlet.setLoadOnStartup(1);
        appServlet.addMapping("/");
    }
}
```

Spring 5 이전 버전의 경우 인터페이스 대신 WebMvcConfigurerAdapter 클래스를 사용해야 한다.

## 3. XML 구성을 사용하는 Spring MVC
Java 구성 대신 순수한 XML 구성을 사용할 수도 있다.

```xml
<context:component-scan base-package="com.baeldung.web.controller" />
<mvc:annotation-driven />
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/view/" />
        <property name="suffix" value=".jsp" />
    </bean>
<mvc:view-controller path="/" view-name="index" />
</beans>
```

순수한 XML 구성을 사용하려면 애플리케이션을 부트스트랩하기 위해 `web.xml` 파일도 추가해야 한다. 자세한 내용은 [여기](https://www.baeldung.com/spring-xml-vs-java-config)를 참고한다.

## 4. 컨트롤러와 뷰
기본 컨트롤러의 예이다.

```java
@Controller
public class SampleController {
    @GetMapping("/sample")
    public String showForm() {
        return "sample";
    }
}
```

해당 JSP 리소스는 `sample.jsp` 파일이다.

```jsp
<html>
   <head></head>

   <body>
      <h1>This is the body of the sample view</h1>
   </body>
</html>
```

JSP 기반 뷰 파일은 프로젝트의 `/WEB-INF` 폴더 아래에 위치하므로 직접적인 URL 액세스가 아닌 Spring 인프라에서만 액세스할 수 있다.

## 5. 부트가 있는 스프링 MVC
Spring Boot는 Spring 플랫폼에 추가되어 독립 실행형 프로덕션 등급 애플리케이션을 시작하고 생성하기가 매우 쉽다. Boot는 Spring을 대체하기 위한 것이 아니라 더 빠르고 쉽게 작업할 수 있도록 하기 위한 것이다.

### 1) Spring Boot Starters
새 프레임워크는 특정 기능에 필요한 모든 기술을 가져올 수 있는 종속성 설명자인 편리한 스타터 종속성을 제공한다.

이들은 더 이상 각 종속성에 대한 버전을 지정할 필요가 없고 대신 스타터가 종속성을 관리할 수 있다는 이점이 있다.

시작하는 가장 빠른 방법은 `spring-boot-starter-parent`를 `pom.xml`에 추가하는 것이다.

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
</parent>
```

### 2) 스프링 부트 진입점
Spring Boot를 사용하여 빌드된 각 애플리케이션은 기본 진입점을 정의하기만 하면 된다.

이것은 일반적으로 `@SpringBootApplication` 주석이 달린 기본 메서드가 있는 Java 클래스이다.

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

이 주석은 다음과 같은 다른 주석을 추가한다.

* `@Configuration`은 클래스를 빈 정의의 소스로 표시한다.

* `@EnableAutoConfiguration`은 프레임워크에 클래스 경로의 종속성을 기반으로 빈을 자동으로 추가하도록 지시한다.

* `@ComponentScan`은 애플리케이션 클래스 이하와 동일한 패키지에서 다른 구성 및 빈을 스캔힌다.

Spring Boot를 사용하면 ViewResolver를 사용하지 않고 Thymeleaf 또는 JSP를 사용하여 프런트 엔드를 설정할 수 있다. `pom.xml`에 `spring-boot-starter-thymeleaf` 종속성을 추가하면 Thymeleaf가 활성화되고 추가 구성이 필요하지 않다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-tutorial](https://www.baeldung.com/spring-mvc-tutorial)
