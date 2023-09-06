---
title: Spring Velocity
author: dejavuhyo
date: 2023-09-06 09:35:00 +0900
categories: [Framework, Spring]
tags: [spring-velocity, velocity, apache-velocity]
---

## 1. Velocity
[Velocity](https://velocity.apache.org/)는 일반 텍스트 파일, SQL, XML, Java 코드 및 기타 여러 유형으로 작업할 수 있는 Apache Software Foundation의 템플릿 엔진이다.

## 2. 메이븐 의존성
다음 종속성을 사용하여 Velocity 지원을 활성화한다.

```xml
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity</artifactId>
    <version>1.7</version>
</dependency>

<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-tools</artifactId>
    <version>2.0</version>
</dependency>
```

## 3. Configuration

### 1) Web Config
`web.xml`을 사용하지 않으려면 Java와 초기화 프로그램을 사용하여 웹 프로젝트를 구성한다.

```java
public class MainWebAppInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext sc) throws ServletException {
        AnnotationConfigWebApplicationContext root = new AnnotationConfigWebApplicationContext();
        root.register(WebConfig.class);

        sc.addListener(new ContextLoaderListener(root));

        ServletRegistration.Dynamic appServlet = 
          sc.addServlet("mvc", new DispatcherServlet(new GenericWebApplicationContext()));
        appServlet.setLoadOnStartup(1);
    }
}
```

전통적인 `web.xml`을 사용할 수도 있다.

```xml
<web-app ...>
    <display-name>Spring MVC Velocity</display-name>
    <servlet>
        <servlet-name>mvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/mvc-servlet.xml</param-value>
     </init-param>
     <load-on-startup>1</load-on-startup>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>mvc</servlet-name>
    <url-pattern>/*</url-pattern>
    </servlet-mapping>
 
    <context-param>
        <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/spring-context.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```

`/*` 경로에 서블릿을 매핑했다.

### 2) Spring Config
Spring 구성이다.

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages= {
  "com.baeldung.mvc.velocity.controller",
  "com.baeldung.mvc.velocity.service" }) 
public class WebConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry
          .addResourceHandler("/resources/**")
          .addResourceLocations("/resources/");
    }
 
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    @Bean
    public ViewResolver viewResolver() {
        VelocityLayoutViewResolver bean = new VelocityLayoutViewResolver();
        bean.setCache(true);
        bean.setPrefix("/WEB-INF/views/");
        bean.setLayoutUrl("/WEB-INF/layouts/layout.vm");
        bean.setSuffix(".vm");
        return bean;
    }
    
    @Bean
    public VelocityConfigurer velocityConfig() {
        VelocityConfigurer velocityConfigurer = new VelocityConfigurer();
        velocityConfigurer.setResourceLoaderPath("/");
        return velocityConfigurer;
    }
}
```

구성의 XML 버전이다.

```xml
<beans ...>
    <context:component-scan base-package="com.baeldung.mvc.velocity.*" />
    <context:annotation-config /> 
    <bean id="velocityConfig" 
      class="org.springframework.web.servlet.view.velocity.VelocityConfigurer">
        <property name="resourceLoaderPath">
            <value>/</value>
        </property>
    </bean> 
    <bean id="viewResolver"
      class="org.springframework.web.servlet.view.velocity.VelocityLayoutViewResolver">
        <property name="cache" value="true" />
        <property name="prefix" value="/WEB-INF/views/" />
        <property name="layoutUrl" value="/WEB-INF/layouts/layout.vm" />
        <property name="suffix" value=".vm" />
    </bean>
</beans>
```

주석이 달린 빈 정의를 찾을 위치를 Spring에 알려준다.

```xml
<context:component-scan base-package="com.baeldung.mvc.velocity.*" />
```

프로젝트에서 주석 기반 구성을 사용할 것임을 나타낸다.

```xml
<context:annotation-config />
```

speedConfig 및 viewResolver 빈을 생성함으로써 VelocityConfigurer에 템플릿을 찾을 위치를 알려주고 VelocityLayoutViewResolver에 뷰와 레이아웃을 찾을 위치를 알려준다.

## 4. Velocity 템플릿
공통 header로 템플릿을 만든다.

```html
<div style="...">
    <div style="float: left">
        <h1>Our tutorials</h1>
    </div>
</div>
```

footer 이다.

```html
<div style="...">
    @Copyright baeldung.com
</div>
```

구문 분석을 통해 위의 조각을 사용할 사이트의 공통 레이아웃을 정의한다.

```html
<html>
    <head>
        <title>Spring & Velocity</title>
    </head>
    <body>
        <div>
            #parse("/WEB-INF/fragments/header.vm")
        </div>  
        <div>
            <!-- View index.vm is inserted here -->
            $screen_content
        </div>  
        <div>
            #parse("/WEB-INF/fragments/footer.vm")
        </div>
    </body>
```

`$screen_content` 변수에 페이지 내용이 포함되어 있는 것을 확인할 수 있다.

마지막으로 주요 콘텐츠에 대한 템플릿을 만든다.

```html
<h1>Index</h1>
 
<h2>Tutorials list</h2>
<table border="1">
    <tr>
        <th>Tutorial Id</th>
        <th>Tutorial Title</th>
        <th>Tutorial Description</th>
        <th>Tutorial Author</th>
    </tr>
    #foreach($tut in $tutorials)
    <tr>
        <td>$tut.tutId</td>
        <td>$tut.title</td>
        <td>$tut.description</td>
        <td>$tut.author</td>
    </tr>
    #end
</table>
```

## 5. Controller
레이아웃에 채워질 콘텐츠로 튜토리얼 목록을 반환하는 간단한 컨트롤러를 만든다.

```java
@Controller
@RequestMapping("/")
public class MainController {
 
    @Autowired
    private ITutorialsService tutService;

    @RequestMapping(value ="/", method = RequestMethod.GET)
    public String defaultPage() {
        return "index";
    }

    @RequestMapping(value ="/list", method = RequestMethod.GET)
    public String listTutorialsPage(Model model) { 
        List<Tutorial> list = tutService.listTutorials();
        model.addAttribute("tutorials", list);
        return "index";
    }
}
```

예제를 로컬에서 액세스할 수 있다(예: `localhost:8080/spring-mvc-velocity/`).

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-with-velocity](https://www.baeldung.com/spring-mvc-with-velocity)
