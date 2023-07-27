---
title: Spring MVC ViewResolver
author: dejavuhyo
date: 2023-07-27 09:00:00 +0900
categories: [Framework, Spring]
tags: [spring-mvc, spring-viewresolver, viewresolver]
---

## 1. 스프링 웹 구성
`@EnableWebMvc`, `@Configuration` 및 `@ComponentScan`으로 주석을 추가한다.

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.baeldung.web")
public class WebConfig implements WebMvcConfigurer {
    // All web configuration will go here
}
```

구성에서 뷰 리졸버를 설정한다.

## 2. InternalResourceViewResolver 추가
ViewResolver를 사용하면 뷰 이름에 대한 접두사 또는 접미사와 같은 속성을 설정하여 최종 뷰 페이지 URL을 생성할 수 있다.

```java
@Bean
public ViewResolver internalResourceViewResolver() {
    InternalResourceViewResolver bean = new InternalResourceViewResolver();
    bean.setViewClass(JstlView.class);
    bean.setPrefix("/WEB-INF/view/");
    bean.setSuffix(".jsp");
    return bean;
}
```

예제를 간단하게 하기 위해 요청을 처리하기 위한 컨트롤러가 필요하지 않다.

구성에 정의된 대로 `/WEB-INF/view` 폴더에 있는 간단한 jsp 페이지만 필요하다.

```jsp
<html>
    <head></head>
    <body>
        <h1>This is the body of the sample view</h1>
    </body>
</html>
```

## 3. BeanNameViewResolver 추가
현재 애플리케이션 컨텍스트에서 뷰 이름을 빈 이름으로 해석하는 ViewResovler의 구현이다. 각각은 ViewXML 또는 Java 구성에서 bean으로 정의될 수 있다.

먼저 이전 구성에 BeanNameViewResolver를 추가한다.

```java
@Bean
public BeanNameViewResolver beanNameViewResolver(){
    return new BeanNameViewResolver();
}
```

ViewResolver가 정의되면 뷰를 렌더링하기 위해 DispatcherServlet에 의해 실행될 수 있도록 View 유형의 빈을 정의해야 한다.

```java
@Bean
public View sample() {
    return new JstlView("/WEB-INF/view/sample.jsp");
}
```

컨트롤러 클래스의 해당 처리기 메서드이다.

```java
@GetMapping("/sample")
public String showForm() {
    return "sample";
}
```

컨트롤러 메서드에서 뷰 이름은 "sample"로 반환되며, 이는 이 핸들러 메서드의 뷰가 `/WEB-INF/view/sample.jsp` URL을 포함하는 JstlView 클래스로 확인됨을 의미한다.

## 4. ViewResolver 연결 및 순서 우선순위 정의
Spring MVC는 또한 다중 뷰 리졸버를 지원한다.

이를 통해 일부 상황에서 특정 보기를 재정의할 수 있다. 구성에 둘 이상의 리졸버를 추가하여 보기 리졸버를 간단히 연결할 수 있다.

이 작업을 마치면 이러한 리졸버에 대한 순서를 정의해야 한다. order 속성은 체인 에서 호출 순서를 정의하는데 사용된다. 순서 속성(가장 큰 순서 번호)이 높을수록 뷰 리졸버가 체인에서 나중에 배치된다.

순서를 정의하기 위해 다음 코드 줄을 뷰 리졸버 구성에 추가할 수 있다.

```java
bean.setOrder(0);
```

InternalResourceViewResolver가 더 높은 순서를 가져야 하므로 순서 우선순위에 주의한다. 이는 매우 명시적인 매핑을 나타내도록 의도되었기 때문이다. 그리고 다른 리졸버의 순서가 더 높으면 InternalResourceViewResolver가 호출되지 않을 수 있다.

## 5. 스프링 부트 사용
Spring Boot로 작업할 때 WebMvcAutoConfiguration은 애플리케이션 컨텍스트에서 InternalResourceViewResolver 및 BeanNameViewResolver 빈을 자동으로 구성한다.

또한 템플릿 엔진에 해당하는 스타터를 추가하면 다른 방식으로 수행해야 하는 수동 구성의 많은 부분이 필요하지 않다.

예를 들어 `pom.xml`에 `spring-boot-starter-thymeleaf` 종속성을 추가하면 Thymeleaf가 활성화되고 추가 구성이 필요하지 않다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
    <version>${spring-boot-starter-thymeleaf.version}</version>
</dependency>
```

이 시작 종속성은 애플리케이션 컨텍스트에서 이름이 thymeleafViewResolver 인 ThymeleafViewResolver 빈을 구성한다. 동일한 이름의 빈을 제공하여 자동 구성된 ThymeleafViewResolver를 재정의할 수 있다.

Thymeleaf 뷰 리졸버는 뷰 이름을 접두사와 접미사로 둘러싸서 작동한다. 접두어와 접미어의 기본값은 각각 `classpath:/templates/`와 `.html`이다.

Spring Boot는 각각 속성 `spring.thymeleaf.prefix`과 `spring.thymeleaf.suffix` 접두사를 설정하여 기본값을 변경할 수 있는 옵션도 제공한다.

마찬가지로, Spring Boot를 사용하여 자동 구성되는 해당 뷰 리졸버를 가져오는데 사용할 수 있는 `groovy-templates`, `freemarker` 및 `mustache` 템플릿 엔진에 대한 스타터 종속성을 가지고 있다.

DispatcherServlet은 애플리케이션 컨텍스트에서 찾은 모든 뷰 리졸버를 사용하고 결과를 얻을 때까지 각각을 시도하므로 이러한 뷰 리졸버의 순서는 추가할 계획이라면 매우 중요하다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-view-resolver-tutorial](https://www.baeldung.com/spring-mvc-view-resolver-tutorial)
