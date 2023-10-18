---
title: Spring MVC Thymeleaf 3.0 새로운 기능
author: dejavuhyo
date: 2023-10-18 22:15:00 +0900
categories: [Framework, Spring]
tags: [spring-thymeleaf, thymeleaf, 스프링-타임리프, 타임리프]
---

## 1. 메이븐 의존성
Thymeleaf를 Spring과 통합하는데 필요한 구성이다. `thymeleaf-spring` 라이브러리 종속성이 필요하다.

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

Spring 4 프로젝트의 경우 `thymeleaf-spring5` 대신 `thymeleaf-spring4` 라이브러리를 사용해야 한다.

## 2. 자바 타임리프 구성
새로운 템플릿 엔진, 뷰 및 템플릿 확인자를 구성해야 한다. 그렇게 하려면 Java 구성 클래스를 업데이트해야 한다.

이를 위해서는 [Java 구성 클래스를 업데이트](https://www.baeldung.com/thymeleaf-in-spring-mvc)해야 한다. 새로운 유형의 리졸버 외에도 템플릿은 Spring 인터페이스 ApplicationContextAware를 구현하고 있다.

```java
@Configuration
@EnableWebMvc
@ComponentScan({ "com.baeldung.thymeleaf" })
public class WebMVCConfig implements WebMvcConfigurer, ApplicationContextAware {

    private ApplicationContext applicationContext;

    // Java setter

    @Bean
    public ViewResolver htmlViewResolver() {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine(htmlTemplateResolver()));
        resolver.setContentType("text/html");
        resolver.setCharacterEncoding("UTF-8");
        resolver.setViewNames(ArrayUtil.array("*.html"));
        return resolver;
    }
    
    @Bean
    public ViewResolver javascriptViewResolver() {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine(javascriptTemplateResolver()));
        resolver.setContentType("application/javascript");
        resolver.setCharacterEncoding("UTF-8");
        resolver.setViewNames(ArrayUtil.array("*.js"));
        return resolver;
    }
    
    @Bean
    public ViewResolver plainViewResolver() {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine(plainTemplateResolver()));
        resolver.setContentType("text/plain");
        resolver.setCharacterEncoding("UTF-8");
        resolver.setViewNames(ArrayUtil.array("*.txt"));
        return resolver;
    }
}
```

위에서 HTML 뷰용, 자바스크립트 파일용, 일반 텍스트 파일용 등 세 가지 뷰 리졸버를 만들었다. Thymeleaf는 파일 이름 확장자(각각 `.html`, `.js` 및 `.txt`)를 확인하여 이를 구별한다.

또한 뷰 이름이 포함된 필수 `String[]` 배열을 생성하는 `array()` 메서드를 사용하기 위해 정적 ArrayUtil 클래스도 생성했다.

다음 부분에서는 템플릿 엔진을 구성해야 한다.

```java
private ISpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
    SpringTemplateEngine engine = new SpringTemplateEngine();
    engine.setTemplateResolver(templateResolver);
    return engine;
}
```

마지막으로 세 가지 별도의 템플릿 확인자를 만들어야 한다.

```java
private ITemplateResolver htmlTemplateResolver() {
    SpringResourceTemplateResolver resolver = new SpringResourceTemplateResolver();
    resolver.setApplicationContext(applicationContext);
    resolver.setPrefix("/WEB-INF/views/");
    resolver.setCacheable(false);
    resolver.setTemplateMode(TemplateMode.HTML);
    return resolver;
}

private ITemplateResolver javascriptTemplateResolver() {
    SpringResourceTemplateResolver resolver = new SpringResourceTemplateResolver();
    resolver.setApplicationContext(applicationContext);
    resolver.setPrefix("/WEB-INF/js/");
    resolver.setCacheable(false);
    resolver.setTemplateMode(TemplateMode.JAVASCRIPT);
    return resolver;
}

private ITemplateResolver plainTemplateResolver() {
    SpringResourceTemplateResolver resolver = new SpringResourceTemplateResolver();
    resolver.setApplicationContext(applicationContext);
    resolver.setPrefix("/WEB-INF/txt/");
    resolver.setCacheable(false);
    resolver.setTemplateMode(TemplateMode.TEXT);
    return resolver;
}
```

테스트를 위해서는 캐시되지 않은 템플릿을 사용하는 것이 더 낫기 때문에 `setCacheable(false)` 메소드를 사용하는 것이 좋다.

Javascript 템플릿은 `/WEB-INF/js/` 폴더에 저장되고 일반 텍스트 파일은 `/WEB-INF/txt/` 폴더에 저장되며 마지막으로 HTML 파일의 경로는 `/WEB-INF/html` 이다.

## 3. 스프링 컨트롤러 구성
새로운 구성을 테스트하기 위해 다음과 같은 Spring 컨트롤러를 만들었다.

```java
@Controller
public class InliningController {

    @RequestMapping(value = "/html", method = RequestMethod.GET)
    public String getExampleHTML(Model model) {
        model.addAttribute("title", "Baeldung");
        model.addAttribute("description", "Thymeleaf tutorial");
        return "inliningExample.html";
    }
    
    @RequestMapping(value = "/js", method = RequestMethod.GET)
    public String getExampleJS(Model model) {
        model.addAttribute("students", StudentUtils.buildStudents());
        return "studentCheck.js";
    }
    
    @RequestMapping(value = "/plain", method = RequestMethod.GET)
    public String getExamplePlain(Model model) {
        model.addAttribute("username", SecurityContextHolder.getContext()
          .getAuthentication().getName());
        model.addAttribute("students", StudentUtils.buildStudents());
        return "studentsList.txt";
    }
}
```

HTML 파일 예제에서는 HTML 태그를 이스케이프하거나 포함하지 않고 새로운 인라인 기능을 사용하는 방법을 보여준다.

JS 예제에서는 학생 정보가 포함된 js 파일을 로드하는 AJAX 요청을 생성한다. 예제에서 StudentUtils 클래스 내에서 간단한 `buildStudents()` 메서드를 사용하고 있다.

마지막으로 일반 텍스트 예에서는 학생 정보를 텍스트 파일로 표시한다. 일반 텍스트 템플릿 모드를 사용하는 일반적인 예는 일반 텍스트 이메일을 보내는데 사용될 수 있다.

추가 기능으로 SecurityContextHolder를 사용하여 기록된 사용자 이름을 얻는다.

## 4. HTML, Js, Text 예제 파일
마지막 부분은 세 가지 다른 유형의 파일을 생성하고 새로운 Thymeleaf 기능의 사용법을 테스트하는 것이다. HTML 파일부터 시작한다.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Inlining example</title>
</head>
<body>
    <p>Title of tutorial: [[${title}]]</p>
    <p>Description: [(${description})]</p>
</body>
</html>
```

이 파일에서는 두 가지 다른 접근 방식을 사용한다. 제목을 표시하기 위해 모든 HTML 태그를 제거하고 텍스트만 표시하는 이스케이프 구문을 사용한다. 설명의 경우 HTML 태그를 유지하기 위해 이스케이프되지 않은 구문을 사용한다. 최종 결과는 다음과 같다.

```html
<p>Title of tutorial: Baeldung</p>
<p>Description: <strong>Thymeleaf</strong> tutorial</p>
```

물론 Thymeleaf라는 단어를 굵은 스타일로 표시하여 브라우저에서 구문 분석한다.

다음으로 js 템플릿 기능을 테스트한다.

```javascript
var count = [[${students.size()}]];
alert("Number of students in group: " + count);
```

JAVASCRIPT 템플릿 모드의 속성은 JavaScript로 이스케이프되지 않는다. 그러면 js 경고가 생성된다. `listStudents.html` 파일에서 jQuery AJAX를 사용하여 이 경고를 로드한다.

```javascript
<script>
    $(document).ready(function() {
        $.ajax({
            url : "/spring-thymeleaf/js",
            });
        });
</script>
```

테스트하려는 마지막 기능이지만 가장 중요한 기능은 일반 텍스트 파일 생성이다. 다음 내용으로 `StudentsList.txt` 파일을 만들었다.

```text
Dear [(${username})],

This is the list of our students:
[# th:each="s : ${students}"]
   - [(${s.name})]. ID: [(${s.id})]
[/]
Thanks,
The Baeldung University
```

마크업 템플릿 모드와 마찬가지로 표준 방언에는 처리 가능한 요소 하나(`[# …]`)와 처리 가능한 속성 집합(`th:text`, `th:utext`, `th:if`, `th:unless`, `th:each` 등)만 포함된다. 결과는 이메일 등에서 사용할 수 있는 텍스트 파일이 된다.

테스트하는 방법은 먼저 브라우저를 사용해 본 다음 기존 JUnit 테스트도 확인하는 것이다.

## 5. 스프링 부트의 타임리프
Spring Boot는 `spring-boot-starter-thymeleaf` 종속성을 추가하여 Thymeleaf에 대한 자동 구성을 제공한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
    <version>2.3.3.RELEASE</version>
</dependency>
```

명시적인 구성은 필요하지 않다. 기본적으로 HTML 파일은 `resources/templates` 위치에 배치되어야 한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-thymeleaf-3](https://www.baeldung.com/spring-thymeleaf-3)
