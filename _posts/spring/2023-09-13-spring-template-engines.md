---
title: Spring 템플릿 엔진
author: dejavuhyo
date: 2023-09-13 07:50:00 +0900
categories: [Framework, Spring]
tags: [spring-template-engines, template-engines, thymeleaf, freemarker, groovy, jade4j, 템플릿-엔진]
---

## 1.Spring 뷰 기술
Spring MVC 애플리케이션의 문제가 한 뷰 기술에서 다른 뷰 기술로 완전히 분리되어 있다는 점을 고려하면 기본적으로 구성 문제이다.

각 뷰 유형을 렌더링하려면 각 기술에 해당하는 ViewResolver 빈을 정의해야 한다. 이는 일반적으로 JSP 파일을 반환하는 것과 동일한 방식으로 `@Controller` 매핑 메서드에서 뷰 이름을 반환할 수 있음을 의미한다.

Java Server Pages와 같은 보다 전통적인 기술과 Spring에서 사용할 수 있는 기본 템플릿 엔진인 `Thymeleaf`, `Groovy`, `FreeMarker`, `Jade`에 대해 설명한다.

이들 각각에 대해 표준 Spring 애플리케이션과 Spring Boot를 사용하여 구축된 애플리케이션 모두에 필요한 구성이다.

## 2. 자바 서버 페이지
JSP는 Java 애플리케이션에 가장 널리 사용되는 뷰 기술 중 하나이며 Spring에서 기본적으로 지원된다. JSP 파일을 렌더링하기 위해 일반적으로 사용되는 ViewResolver 빈 유형은 InternalResourceViewResolver이다.

```java
@EnableWebMvc
@Configuration
public class ApplicationConfiguration implements WebMvcConfigurer {
    @Bean
    public ViewResolver jspViewResolver() {
        InternalResourceViewResolver bean = new InternalResourceViewResolver();
        bean.setPrefix("/WEB-INF/views/");
        bean.setSuffix(".jsp");
        return bean;
    }
}
```

`/WEB-INF/views` 위치에 JSP 파일 생성을 시작할 수 있다.

```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<html>
    <head>
        <meta http-equiv="Content-Type" 
          content="text/html; charset=ISO-8859-1">
        <title>User Registration</title>
    </head>
    <body>
        <form:form method="POST" modelAttribute="user">
            <form:label path="email">Email: </form:label>
            <form:input path="email" type="text"/>
            <form:label path="password">Password: </form:label>
            <form:input path="password" type="password" />
            <input type="submit" value="Submit" />
        </form:form>
    </body>
</html>
```

Spring Boot 애플리케이션에 파일을 추가하는 경우 `ApplicationConfiguration` 클래스 대신 `application.properties` 파일에 다음 속성을 정의할 수 있다.

```properties
spring.mvc.view.prefix: /WEB-INF/views/
spring.mvc.view.suffix: .jsp
```

이러한 속성을 기반으로 Spring Boot는 필요한 ViewResolver를 자동 구성한다.

## 3. Thymeleaf
[Thymeleaf](https://www.thymeleaf.org/)는 HTML, XML, 텍스트, JavaScript 또는 CSS 파일을 처리할 수 있는 Java 템플릿 엔진이다. 다른 템플릿 엔진과 달리 Thymeleaf는 템플릿을 프로토타입으로 사용할 수 있다. 즉, 템플릿을 정적 파일로 볼 수 있다.

### 1) 메이븐 종속성
Thymeleaf를 Spring과 통합하려면 thymeleaf 및 thymeleaf-spring4 종속성을 추가해야 한다.

```xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf</artifactId>
    <version>3.0.11.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
    <version>3.0.11.RELEASE</version>
</dependency>
```

Spring 4 프로젝트가 있는 경우 `thymeleaf-spring4`를 추가해야 한다.

### 2) Spring 구성
다음으로 SpringTemplateEngine 빈과 뷰 파일의 위치와 유형을 지정하는 TemplateResolver 빈이 필요한 구성을 추가해야 한다.

SpringResourceTemplateResolver는 Spring의 리소스 확인 메커니즘과 통합된다.

```java
@Configuration
@EnableWebMvc
public class ThymeleafConfiguration {
 
    @Bean
    public SpringTemplateEngine templateEngine() {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(thymeleafTemplateResolver());
        return templateEngine;
    }

    @Bean
    public SpringResourceTemplateResolver thymeleafTemplateResolver() {
        SpringResourceTemplateResolver templateResolver 
          = new SpringResourceTemplateResolver();
        templateResolver.setPrefix("/WEB-INF/views/");
        templateResolver.setSuffix(".html");
        templateResolver.setTemplateMode("HTML5");
        return templateResolver;
    }
}
```

또한 ThymeleafViewResolver 유형의 ViewResolver 빈이 필요하다.

```java
@Bean
public ThymeleafViewResolver thymeleafViewResolver() {
    ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
    viewResolver.setTemplateEngine(templateEngine());
    return viewResolver;
}
```

### 3) Thymeleaf 템플릿
`WEB-INF/views` 위치에 HTML 파일을 추가할 수 있다.

```html
<html>
    <head>
        <meta charset="ISO-8859-1" />
        <title>User Registration</title>
    </head>
    <body>
        <form action="#" th:action="@{/register}" 
          th:object="${user}" method="post">
            Email:<input type="text" th:field="*{email}" />
            Password:<input type="password" th:field="*{password}" />
            <input type="submit" value="Submit" />
        </form>
    </body>
</html>
```

Thymeleaf 템플릿은 HTML 템플릿과 구문이 매우 유사하다.

Spring 애플리케이션에서 Thymeleaf를 사용할 때 사용할 수 있는 일부 기능은 다음과 같다.

* 양식 동작 정의 지원

* 양식 입력을 데이터 모델에 바인딩

* 양식 입력에 대한 유효성 검사

* 메시지 소스의 값 표시

* 템플릿 조각 렌더링

> 참고: [Spring MVC Thymeleaf](https://www.baeldung.com/thymeleaf-in-spring-mvc)

### 4) Spring Boot의 Thymeleaf
Spring Boot는 `spring-boot-starter-thymeleaf` 종속성을 추가하여 Thymeleaf에 대한 자동 구성을 제공한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
    <version>2.5.6</version>
</dependency>
```

명시적인 구성은 필요하지 않다. 기본적으로 HTML 파일은 `resources/templates` 위치에 배치되어야 한다.

## 4. FreeMarker
[FreeMarker](http://freemarker.org/)는 Apache Software Foundation에서 구축한 Java 기반 템플릿 엔진이다. 웹 페이지를 생성하는데 사용할 수 있을 뿐만 아니라 소스 코드, XML 파일, 구성 파일, 이메일 및 기타 텍스트 기반 형식도 생성할 수 있다.

생성은 FreeMarker 템플릿 언어를 사용하여 작성된 템플릿 파일을 기반으로 수행된다.

### 1) 메이븐 종속성
프로젝트에서 템플릿 사용을 시작하려면 freemarker 종속성이 필요하다.

```xml
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.23</version>
</dependency>
```

Spring 통합을 위해서는 `spring-context-support` 종속성도 필요하다.

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>5.2.8.RELEASE</version>
</dependency>
```

### 2) Spring 구성
FreeMarker를 Spring MVC와 통합하려면 템플릿 파일의 위치를 ​​지정하는 FreemarkerConfigurer 빈을 정의해야 한다.

```java
@Configuration
@EnableWebMvc
public class FreemarkerConfiguration {
 
    @Bean 
    public FreeMarkerConfigurer freemarkerConfig() { 
        FreeMarkerConfigurer freeMarkerConfigurer = new FreeMarkerConfigurer(); 
        freeMarkerConfigurer.setTemplateLoaderPath("/WEB-INF/views/");
        return freeMarkerConfigurer; 
    }
}
```

다음으로 FreeMarkerViewResolver 유형의 적절한 ViewResolver 빈을 정의해야 한다.

```java
@Bean 
public FreeMarkerViewResolver freemarkerViewResolver() {
    FreeMarkerViewResolver resolver = new FreeMarkerViewResolver();
    resolver.setCache(true);
    resolver.setPrefix("");
    resolver.setSuffix(".ftl");
    return resolver;
}
```

### 3) FreeMarker 템플릿
`WEB-INF/views` 위치에서 FreeMarker를 사용하여 HTML 템플릿을 만들 수 있다.

```html
<#import "/spring.ftl" as spring/>
<html>
    <head>
        <meta charset="ISO-8859-1" />
        <title>User Registration</title>
    </head>
    <body>
        <form action="register" method="post">
            <@spring.bind path="user" />
            Email: <@spring.formInput "user.email"/>
            Password: <@spring.formPasswordInput "user.password"/>
            <input type="submit" value="Submit" />
        </form>
    </body>
</html>
```

위의 예에서는 양식 입력을 데이터 모델에 바인딩하는 것을 포함하여 FreeMarker의 양식 작업을 위해 Spring에서 정의한 매크로 세트를 가져왔다.

또한 FreeMarker 템플릿 언어에는 컬렉션, 흐름 제어 구조, 논리 연산자, 문자열 형식 지정 및 구문 분석, 숫자 및 기타 다양한 기능을 사용하기 위한 수많은 태그, 지시문 및 표현식이 포함되어 있다.

### 4) Spring Boot의 FreeMarker
Spring Boot 애플리케이션에서는 `spring-boot-starter-freemarker` 종속성을 사용하여 필요한 구성을 단순화할 수 있다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
    <version>2.5.6</version>
</dependency>
```

스타터는 필요한 자동 구성을 추가한다. 템플릿 파일을 `resources/templates` 폴더에 배치한다.

## 5. Groovy
Groovy Markup Template Engine을 사용하여 Spring MVC 뷰를 생성할 수도 있다. 이 엔진은 빌더 구문을 기반으로 하며 모든 텍스트 형식을 생성하는 데 사용할 수 있다.

### 1) 메이븐 종속성
`groovy-templates` 종속성을 `pom.xml`에 추가한다.

```xml
<dependency>
    <groupId>org.codehaus.groovy</groupId>
    <artifactId>groovy-templates</artifactId>
    <version>2.4.12</version>
</dependency>
```

### 2) Spring 구성
마크업 템플릿 엔진을 Spring MVC와 통합 하려면 `GroovyMarkupConfigurer` 빈과 `GroovyMarkupViewResolver` 유형의 ViewResolver를 정의해야 한다.

```java
@Configuration
@EnableWebMvc
public class GroovyConfiguration {
 
    @Bean
    public GroovyMarkupConfigurer groovyMarkupConfigurer() {
        GroovyMarkupConfigurer configurer = new GroovyMarkupConfigurer();
        configurer.setResourceLoaderPath("/WEB-INF/views/");
        return configurer;
    }
    
    @Bean
    public GroovyMarkupViewResolver thymeleafViewResolver() {
        GroovyMarkupViewResolver viewResolver = new GroovyMarkupViewResolver();
        viewResolver.setSuffix(".tpl");
        return viewResolver;
    }
}
```

### 3) Groovy Markup 템플릿
템플릿은 Groovy 언어로 작성되었으며 다음과 같은 몇 가지 특징을 가지고 있다.

* 바이트코드로 컴파일된다.

* 조각과 레이아웃에 대한 지원이 포함되어 있다.

* 국제화를 지원한다.

* 렌더링이 빠르다.

데이터 바인딩이 포함된 "사용자 등록" 양식을 위한 Groovy 템플릿을 만든다.

```html
yieldUnescaped '<!DOCTYPE html>'
html(lang:'en') {
    head {
        meta('http-equiv':'"Content-Type" ' +
          'content="text/html; charset=utf-8"')
        title('User Registration')
    }
    body {
        form (id:'userForm', action:'register', method:'post') {
            label (for:'email', 'Email')
            input (name:'email', type:'text', value:user.email?:'')
            label (for:'password', 'Password')
            input (name:'password', type:'password', value:user.password?:'')
            div (class:'form-actions') {
                input (type:'submit', value:'Submit')
            }
        }
    }
}
```

### 4) Spring Boot의 Groovy Template Engine
Spring Boot에는 `spring-boot-starter-groovy-templates` 종속성을 포함하여 추가되는 Groovy 템플릿 엔진에 대한 자동 구성이 포함되어 있다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-groovy-templates</artifactId>
    <version>2.5.6</version>
</dependency>
```

템플릿의 기본 위치는 `/resources/templates`이다.

## 6. Jade4j
[Jade4j](https://github.com/neuland/jade4j)는 Javascript용 Pug 템플릿 엔진(원래 Jade라고 함)을 Java로 구현한 것이다. Jade4j 템플릿은 HTML 파일을 생성하는데 사용할 수 있다.

### 1) 메이븐 종속성
Spring 통합을 위해서는 `spring-jade4j` 종속성이 필요하다.

```xml
<dependency>
    <groupId>de.neuland-bfi</groupId>
    <artifactId>spring-jade4j</artifactId>
    <version>1.2.5</version>
</dependency>
```

### 2) Spring 구성
Spring과 함께 Jade4j를 사용하려면 템플릿의 위치를 ​​구성하는 `SpringTemplateLoader` 빈과 `JadeConfiguration` 빈을 정의해야 한다.

```java
@Configuration
@EnableWebMvc
public class JadeTemplateConfiguration {
 
    @Bean
    public SpringTemplateLoader templateLoader() {
        SpringTemplateLoader templateLoader 
          = new SpringTemplateLoader();
        templateLoader.setBasePath("/WEB-INF/views/");
        templateLoader.setSuffix(".jade");
        return templateLoader;
    }
 
    @Bean
    public JadeConfiguration jadeConfiguration() {
        JadeConfiguration configuration 
          = new JadeConfiguration();
        configuration.setCaching(false);
        configuration.setTemplateLoader(templateLoader());
        return configuration;
    }
}
```

일반적인 ViewResolver 빈이 필요하다. 이 경우 JadeViewResolver 유형이다.

```java
@Bean
public ViewResolver viewResolver() {
    JadeViewResolver viewResolver = new JadeViewResolver();
    viewResolver.setConfiguration(jadeConfiguration());
    return viewResolver;
}
```

### 2) Jade4j 템플릿
Jade4j 템플릿은 사용하기 쉬운 공백 구분 구문이 특징이다.

```text
doctype html
html
  head
    title User Registration
  body
    form(action="register" method="post" )
      label(for="email") Email:
      input(type="text" name="email")
      label(for="password") Password:
      input(type="password" name="password")
      input(type="submit" value="Submit")
```

Spring Boot는 Jade4j 스타터를 제공하지 않으므로 Boot 프로젝트에서는 위에서 정의한 것과 동일한 Spring 구성을 추가해야 한다.

## 7. 기타 템플릿 엔진
[Velocity](https://velocity.apache.org/)는 오래된 템플릿 엔진으로 매우 복잡하지만 Spring이 버전 4.3부터 더 이상 사용하지 않고 Spring 5.0.1에서 완전히 제거된다는 단점이 있다.

[JMustache](https://github.com/samskivert/jmustache)는 `spring-boot-starter-mustache` 종속성을 사용하여 Spring Boot 애플리케이션에 쉽게 통합할 수 있는 템플릿 엔진이다.

[Pebble](https://pebbletemplates.io/)에는 라이브러리 내에 Spring 및 Spring Boot에 대한 지원이 포함되어 있다.

[Nashorn](https://openjdk.org/projects/nashorn/)과 같은 `JSR-223` 스크립트 엔진 위에서 실행되는 Handlebars 또는 React와 같은 다른 템플릿 라이브러리도 사용할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-apache-tiles](https://www.baeldung.com/spring-mvc-apache-tiles)
