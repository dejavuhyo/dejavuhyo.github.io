---
title: Spring FreeMarker
author: dejavuhyo
date: 2023-09-11 09:15:00 +0900
categories: [Framework, Spring]
tags: [spring-freemarker, freemarker, apache-freemarker]
---

## 1. FreeMarker
FreeMarker는 Apache Software Foundation의 Java 기반 템플릿 엔진이다. 다른 템플릿 엔진과 마찬가지로 FreeMarker는 MVC 패턴을 따르는 애플리케이션에서 HTML 웹 페이지를 지원하도록 설계되었다.

## 2. 메이븐 의존성
Maven 기반 프로젝트이므로 먼저 `pom.xml`에 필요한 종속성을 추가 한다.

```xml
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.23</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>${spring.version}</version>
</dependency>
```

## 3. Configurations
주석 기반 Spring 프로젝트이므로 XML 기반 구성을 보여주지 않는다.

### 1) Spring Web 구성
웹 구성 요소를 구성하는 클래스를 만든다. 이를 위해서는 `@EnableWebMvc`, `@Configuration` 및 `@ComponentScan`으로 클래스에 주석을 단다.

```java
@EnableWebMvc
@Configuration
@ComponentScan({"com.baeldung.freemarker"})
public class SpringWebConfig extends WebMvcConfigurerAdapter {
    // All web configuration will go here.
}
```

### 2) ViewResolver 구성
Spring MVC Framework는 뷰 이름을 실제 뷰에 매핑하는 ViewResolver 인터페이스를 제공한다. spring-webmvc 종속성에 속하는 FreeMarkerViewResolver 인스턴스를 생성한다.

해당 개체는 런타임에 사용될 필수 값으로 구성되어야 한다. 예를 들어, `.ftl`로 끝나는 뷰에 대해 FreeMarker를 사용하도록 뷰 확인자를 구성한다.

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

또한 여기에서 캐싱 모드를 제어하는 ​​방법도 확인한. 이 모드는 디버깅 및 개발 시에만 비활성화해야 한다.

### 3) FreeMarker 템플릿 경로 구성
웹 컨텍스트에서 템플릿이 있는 위치를 나타내는 템플릿 경로를 설정한다.

```java
@Bean 
public FreeMarkerConfigurer freemarkerConfig() { 
    FreeMarkerConfigurer freeMarkerConfigurer = new FreeMarkerConfigurer(); 
    freeMarkerConfigurer.setTemplateLoaderPath("/WEB-INF/views/ftl/");
    return freeMarkerConfigurer; 
}
```

### 4) Spring 컨트롤러 구성
Spring Controller를 사용하여 표시용 FreeMarker 템플릿을 처리 할 수 있다. 이것은 단순히 일반적인 Spring Controller이다.

```java
@RequestMapping(value = "/cars", method = RequestMethod.GET)
public String init(@ModelAttribute("model") ModelMap model) {
    model.addAttribute("carList", carList);
    return "index";
}
```

이전에 정의된 FreeMarkerViewResolver 및 경로 구성은 뷰 이름 인덱스를 적절한 FreeMarker 뷰로 변환하는 작업을 담당한다.

## 4. FreeMarker HTML 템플릿

### 1) 간단한 HTML 템플릿 보기 만들기
FreeMarker를 사용하여 HTML 템플릿을 만든다. 이 예에서는 모델에 자동차 목록을 추가했다. FreeMarker는 해당 목록에 액세스하고 해당 내용을 반복하여 표시할 수 있다.

`/cars` URI에 대한 요청이 이루어지면 Spring은 제공된 모델을 사용하여 템플릿을 처리한다. 템플릿에서 `#list` 지시문은 FreeMarker가 현재 요소를 참조하고 해당 블록 내의 콘텐츠를 렌더링하기 위해 car를 사용하여 모델의 carList 개체를 반복해야 함을 나타낸다.

아래 예제에는 carList의 각 요소 속성을 참조하는 FreeMarker 표현식도 포함되어 있다. 또는 예를 들어 현재 자동차 요소의 make 속성을 표시하려면 `${car.make}` 표현식을 사용한다.

```html
<div id="header">
  <h2>FreeMarker Spring MVC Hello World</h2>
</div>
<div id="content">
  <fieldset>
    <legend>Add Car</legend>
    <form name="car" action="add" method="post">
      Make : <input type="text" name="make" /><br/>
      Model: <input type="text" name="model" /><br/>
      <input type="submit" value="Save" />
    </form>
  </fieldset>
  <br/>
  <table class="datatable">
    <tr>
      <th>Make</th>
      <th>Model</th>
    </tr>
    <#list model["carList"] as car>
      <tr>
        <td>${car.make}</td>
        <td>${car.model}</td>
      </tr>
    </#list>
  </table>
</div>
```

CSS로 출력 스타일을 지정한 후 처리된 FreeMarker 템플릿은 자동차의 양식과 목록을 생성한다.

![html-template-view](/assets/img/2023-09-11-spring-freemarker/html-template-view.png)

## 5. 스프링 부트
Spring Boot를 사용하는 경우 spring-boot-starter-freemarker 종속성을 간단히 가져올 수 있다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
    <version>2.3.4.RELEASE</version>
</dependency>
```

그리고 `src/main/resources/templates` 아래에 템플릿 파일을 추가하기만 하면 된다. Spring Boot는 FreeMarkerConfigurer 및 FreeMarkerViewResolver와 같은 다른 기본 구성을 담당한다.

## [출처 및 참고]
* [https://www.baeldung.com/freemarker-in-spring-mvc-tutorial](https://www.baeldung.com/freemarker-in-spring-mvc-tutorial)
