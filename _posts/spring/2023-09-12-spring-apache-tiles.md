---
title: Spring과 Apache Tiles 통합
author: dejavuhyo
date: 2023-09-12 07:35:00 +0900
categories: [Framework, Spring]
tags: [spring-tiles, apache-tiles, tiles]
---

## 1. Apache Tiles
Apache Tiles는 Composite 디자인 패턴을 기반으로 구축된 무료 오픈 소스 템플릿 프레임워크이다.

컴포지트 디자인 패턴(Composite Design Pattern)은 객체를 트리 구조로 구성하여 전체 부분 계층을 표현하는 구조 패턴의 일종으로, 개별 객체와 객체의 구성을 균일하게 취급하는 패턴이다. 즉, Tiles에서는 Tiles라는 하위 뷰의 구성을 모아서 페이지를 구성한다.

다른 프레임워크에 비해 이 프레임워크의 장점은 다음과 같다.

* 재사용 성

* 구성이 용이함

* 낮은 성능 오버헤드

## 2. 종속성 구성
 pom.xml 에 필요한 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-jsp</artifactId>
    <version>3.0.8</version>
</dependency>
```

## 3. 타일 레이아웃 파일
템플릿 정의를 정의해야 하며 특히 각 페이지에 따라 해당 특정 페이지에 대한 템플릿 정의를 덮어쓰게 된다.

```xml
<tiles-definitions>
    <definition name="template-def" 
           template="/WEB-INF/views/tiles/layouts/defaultLayout.jsp">  
        <put-attribute name="title" value="" />  
        <put-attribute name="header" 
           value="/WEB-INF/views/tiles/templates/defaultHeader.jsp" />  
        <put-attribute name="menu" 
           value="/WEB-INF/views/tiles/templates/defaultMenu.jsp" />  
        <put-attribute name="body" value="" />  
        <put-attribute name="footer" 
           value="/WEB-INF/views/tiles/templates/defaultFooter.jsp" />  
    </definition>  
    <definition name="home" extends="template-def">  
        <put-attribute name="title" value="Welcome" />  
        <put-attribute name="body" 
           value="/WEB-INF/views/pages/home.jsp" />  
    </definition>  
</tiles-definitions>
```

## 4. ApplicationConfiguration 및 기타 클래스
구성의 일부로 ApplicationInitializer, ApplicationController 및 ApplicationConfiguration 이라는 세 가지 특정 Java 클래스를 생성한다.

* ApplicationInitializer가 ApplicationConfiguration 클래스에 지정된 필요한 구성을 초기화하고 확인한다.

* ApplicationConfiguration 클래스에는 Spring MVC를 Apache Tiles 프레임워크와 통합하기 위한 구성이 포함되어 있다.

* ApplicationController 클래스는 `tiles.xml` 파일과 동기화되어 작동하며 수신 요청에 따라 필요한 페이지로 리디렉션된다.

클래스의 실제 동작이다.

```java
@Controller
@RequestMapping("/")
public class TilesController {
    @RequestMapping(
      value = { "/"}, 
      method = RequestMethod.GET)
    public String homePage(ModelMap model) {
        return "home";
    }
    @RequestMapping(
      value = { "/apachetiles"}, 
      method = RequestMethod.GET)
    public String productsPage(ModelMap model) {
        return "apachetiles";
    }
 
    @RequestMapping(
      value = { "/springmvc"},
      method = RequestMethod.GET)
    public String contactUsPage(ModelMap model) {
        return "springmvc";
    }
}
```

```java
public class WebInitializer implements WebApplicationInitializer {
 public void onStartup(ServletContext container) throws ServletException {

        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        
        ctx.register(TilesApplicationConfiguration.class);

        container.addListener(new ContextLoaderListener(ctx));

        ServletRegistration.Dynamic servlet = container.addServlet(
          "dispatcher", new DispatcherServlet(ctx));
        servlet.setLoadOnStartup(1);
        servlet.addMapping("/");
    }
}
```

Spring MVC 애플리케이션에서 타일을 구성하는데 중요한 역할을 하는 두 가지 중요한 클래스가 있다. 그것은 TilesConfigurer 및 TilesViewResolver이다.

* TilesConfigurer는 타일 구성 파일에 대한 경로를 제공하여 Tiles 프레임워크를 Spring 프레임워크와 연결하는데 도움을 준다.

* TilesViewResolver는 타일 뷰를 해결하기 위해 Spring API에서 제공하는 어댑터 클래스 중 하나이다.

ApplicationConfiguration 클래스에서는 TilesConfigurer 및 TilesViewResolver 클래스를 사용하여 통합을 달성한다.

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.baeldung.spring.controller.tiles")
public class TilesApplicationConfiguration implements WebMvcConfigurer {
    @Bean
    public TilesConfigurer tilesConfigurer() {
        TilesConfigurer tilesConfigurer = new TilesConfigurer();
        tilesConfigurer.setDefinitions(
          new String[] { "/WEB-INF/views/**/tiles.xml" });
        tilesConfigurer.setCheckRefresh(true);
        
        return tilesConfigurer;
    }
    
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        TilesViewResolver viewResolver = new TilesViewResolver();
        registry.viewResolver(viewResolver);
    }
    
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
          .addResourceLocations("/static/");
    }
}
```

## 5. Tiles 템플릿 파일
`Tiles.xml`에 정의된 특정 템플릿 파일을 생성한다.

특정 페이지를 구축하기 위한 기반으로 사용할 수 있는 레이아웃 조각을 찾는다.

```html
<html>
    <head>
        <meta 
          http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title><tiles:getAsString name="title" /></title>
        <link href="<c:url value='/static/css/app.css' />" 
            rel="stylesheet">
        </link>
    </head>
    <body>
        <div class="flex-container">
            <tiles:insertAttribute name="header" />
            <tiles:insertAttribute name="menu" />
        <article class="article">
            <tiles:insertAttribute name="body" />
        </article>
        <tiles:insertAttribute name="footer" />
        </div>
    </body>
</html>
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-apache-tiles](https://www.baeldung.com/spring-mvc-apache-tiles)
