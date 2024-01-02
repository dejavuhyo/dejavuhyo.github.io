---
title: Spring Thymeleaf
author: dejavuhyo
date: 2023-07-31 08:50:00 +0900
categories: [Framework, Spring]
tags: [spring-thymeleaf, thymeleaf]
---

## 1. Thymeleaf와 Spring의 통합
 pring과 연동하기 위해 필요한 설정이다. 통합을 위해서는 thymeleaf-spring 라이브러리가 필요하다.

Maven POM 파일에 다음 종속성을 추가한다.

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

Spring 4 프로젝트의 경우 thymeleaf-spring5 대신 thymeleaf-spring4 라이브러리를 사용해야 한다.

SpringTemplateEngine 클래스는 모든 구성 단계를 수행한다.

Java 구성 파일에서 이 클래스를 bean으로 구성할 수 있다.

```java
@Bean
@Description("Thymeleaf Template Resolver")
public ServletContextTemplateResolver templateResolver() {
    ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver();
    templateResolver.setPrefix("/WEB-INF/views/");
    templateResolver.setSuffix(".html");
    templateResolver.setTemplateMode("HTML5");

    return templateResolver;
}

@Bean
@Description("Thymeleaf Template Engine")
public SpringTemplateEngine templateEngine() {
    SpringTemplateEngine templateEngine = new SpringTemplateEngine();
    templateEngine.setTemplateResolver(templateResolver());
    templateEngine.setTemplateEngineMessageSource(messageSource());
    return templateEngine;
}
```

templateResolver 빈 특성 접두어와 접미어는 각각 webapp 디렉토리 내의 보기 페이지 위치와 해당 파일 이름 확장자를 나타낸다.

Spring MVC의 ViewResolver 인터페이스는 컨트롤러가 반환한 뷰 이름을 실제 뷰 객체에 매핑한다. ThymeleafViewResolver는 ViewResolver 인터페이스를 구현하며 뷰 이름이 주어지면 렌더링할 Thymeleaf 뷰를 결정하는데 사용된다.

통합의 마지막 단계는 ThymeleafViewResolver를 빈으로 추가하는 것이다.

```java
@Bean
@Description("Thymeleaf View Resolver")
public ThymeleafViewResolver viewResolver() {
    ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
    viewResolver.setTemplateEngine(templateEngine());
    viewResolver.setOrder(1);
    return viewResolver;
}
```

## 2. 스프링 부트의 Thymeleaf
Spring Boot는 [spring-boot-starter-thymeleaf](https://central.sonatype.com/artifact/org.springframework.boot/spring-boot-starter-thymeleaf) 종속성을 추가하여 Thymeleaf 에 대한 자동 구성을 제공한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
    <version>2.3.3.RELEASE</version>
</dependency>
```

명시적인 구성이 필요하지 않다. 기본적으로 HTML 파일은 `resources/templates` 위치에 있어야 한다.

## 3. 메시지 소스(속성 파일)에서 값 표시
`th:text="#{key}"` 태그 속성을 사용하여 속성 파일의 값을 표시할 수 있다.

이것이 작동하려면 특성 파일을 messageSource 빈으로 구성해야 한다.

```java
@Bean
@Description("Spring Message Resolver")
public ResourceBundleMessageSource messageSource() {
    ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
    messageSource.setBasename("messages");
    return messageSource;
}
```

다음은 `welcome.messag`e 키와 관련된 값을 표시하는 Thymeleaf HTML 코드이다.

```html
<span th:text="#{welcome.message}" />
```

## 4. 모델 속성 표시

### 1) 단순 속성
`th:text="${attributename}"` 태그 속성을 사용하여 모델 속성의 값을 표시할 수 있다.

컨트롤러 클래스에 이름이 serverTime인 모델 특성을 추가한다.

```java
model.addAttribute("serverTime", dateFormat.format(new Date()));
```

다음은 serverTime 속성 값을 표시하는 HTML 코드이다.

```html
Current time is <span th:text="${serverTime}" />
```

### 2) 컬렉션 속성
모델 속성이 객체 모음인 경우 `th:each` 태그 속성을 사용하여 반복할 수 있다.

id와 name 이라는 두 개의 필드가 있는 Student 모델 클래스를 정의한다.

```java
public class Student implements Serializable {
    private Integer id;
    private String name;
    // standard getters and setters
}
```

이제 컨트롤러 클래스에 모델 속성으로 학생 목록을 추가한다.

```java
List<Student> students = new ArrayList<Student>();
// logic to build student data
model.addAttribute("students", students);
```

마지막으로 Thymeleaf 템플릿 코드를 사용하여 학생 목록을 반복하고 모든 필드 값을 표시할 수 있다.

```html
<tbody>
    <tr th:each="student: ${students}">
        <td th:text="${student.id}" />
        <td th:text="${student.name}" />
    </tr>
</tbody>
```

## 5. 조건부 평가

### 1) if 그리고 unless
`th:if="${condition}"` 속성을 사용하여 조건이 충족되면 뷰의 섹션을 표시한다. 그리고 `th:unless="${condition}"` 속성을 사용하여 조건이 충족되지 않는 경우 뷰의 섹션을 표시한다.

Student 모델에 성별 필드를 추가한다.

```java
public class Student implements Serializable {
    private Integer id;
    private String name;
    private Character gender;
    
    // standard getters and setters
}
```

이 필드에 학생의 성별을 나타내는 두 가지 가능한 값(M 또는 F)이 있다고 가정한다.

단일 문자 대신 "Male" 또는 "Female"이라는 단어를 표시하려면 다음 Thymeleaf 코드를 사용하면 된다.

```html
<td>
    <span th:if="${student.gender} == 'M'" th:text="Male" /> 
    <span th:unless="${student.gender} == 'M'" th:text="Female" />
</td>
```

### 2) switch 그리고 case
`th:switch` 및 `th:case` 특성을 사용하여 switch문 구조를 사용하여 조건부로 콘텐츠를 표시한다.

`th:switch` 및 `th:case` 속성을 사용하여 이전 코드를 다시 작성한다.

```html
<td th:switch="${student.gender}">
    <span th:case="'M'" th:text="Male" /> 
    <span th:case="'F'" th:text="Female" />
</td>
```

## 6. 사용자 입력 처리
`th:action="@{url}"` 및 `th:object="${object}"` 속성을 사용하여 양식 입력을 처리할 수 있다. `th:action`을 사용하여 양식 작업 URL을 제공하고 `th:object`를 사용하여 제출된 양식 데이터가 바인딩될 개체를 지정한다.

개별 필드는 `th:field="*{name}"` 속성을 사용하여 매핑된다. 여기서 이름은 개체의 일치하는 속성이다.

Student 클래스의 경우 입력 양식을 만들 수 있다.

```html
<form action="#" th:action="@{/saveStudent}" th:object="${student}" method="post">
    <table border="1">
        <tr>
            <td><label th:text="#{msg.id}" /></td>
            <td><input type="number" th:field="*{id}" /></td>
        </tr>
        <tr>
            <td><label th:text="#{msg.name}" /></td>
            <td><input type="text" th:field="*{name}" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form>
```

위의 코드에서 `/saveStudent`는 양식 작업 URL이고 학생은 제출된 양식 데이터를 보유하는 개체이다.

StudentController 클래스는 양식 제출을 처리한다.

```java
@Controller
public class StudentController {
    @RequestMapping(value = "/saveStudent", method = RequestMethod.POST)
    public String saveStudent(@ModelAttribute Student student, BindingResult errors, Model model) {
        // logic to process input data
    }
}
```

`@RequestMapping` 주석은 컨트롤러 메서드를 양식에 제공된 URL과 매핑한다. 주석이 달린 `saveStudent()` 메서드는 제출된 양식에 필요한 처리를 수행한다. 마지막으로 `@ModelAttribute` 주석은 양식 필드를 학생 개체에 바인딩한다.

## 7. 유효성 검사 오류 표시
`#fields.hasErrors()` 함수를 사용하여 필드에 유효성 검사 오류가 있는지 확인할 수 있다. 그리고 `#fields.errors()` 함수를 사용하여 특정 필드에 대한 오류를 표시한다. 필드 이름은 이 두 함수의 입력 매개변수이다.

양식의 각 필드에 대한 오류를 반복하고 표시하는 HTML 코드를 확인한다.

```html
<ul>
    <li th:each="err : ${#fields.errors('id')}" th:text="${err}" />
    <li th:each="err : ${#fields.errors('name')}" th:text="${err}" />
</ul>
```

위의 함수는 필드 이름 대신 와일드 카드 문자 `*` 또는 상수 all을 허용하여 모든 필드를 나타낸다. `th:each` 속성을 사용하여 각 필드에 존재할 수 있는 여러 오류를 반복했다.

다음은 와일드카드 `*`를 사용하여 재작성된 이전 HTML 코드이다.

```html
<ul>
    <li th:each="err : ${#fields.errors('*')}" th:text="${err}" />
</ul>
```

여기서 상수 all을 사용하고 있다.

```html
<ul>
    <li th:each="err : ${#fields.errors('all')}" th:text="${err}" />
</ul>
```

마찬가지로 전역 상수를 사용하여 Spring에서 전역 오류를 표시할 수 있다.

전역 오류를 표시하는 HTML 코드는 다음과 같다.

```html
<ul>
    <li th:each="err : ${#fields.errors('global')}" th:text="${err}" />
</ul>
```

또한 `th:errors` 속성을 사용하여 오류 메시지를 표시할 수 있다.

양식에 오류를 표시하는 이전 코드는 `th:errors` 속성을 사용하여 다시 작성할 수 있다.

```html
<ul>
    <li th:errors="*{id}" />
    <li th:errors="*{name}" />
</ul>
```

## 8. 변환 사용
이중 괄호 구문 `{{}}`을 사용하여 표시할 데이터 형식을 지정한다. 이는 컨텍스트 파일의 conversionService bean에서 해당 유형의 필드에 대해 구성된 포맷터를 사용한다.

Student 클래스에서 이름 필드의 형식을 지정하는 방법이다.

```html
<tr th:each="student: ${students}">
    <td th:text="${{student.name}}" />
</tr>
```

위의 코드는 WebMvcConfigurer 인터페이스에서 `addFormatters()` 메서드를 재정의하여 구성된 NameFormatter 클래스를 사용한다.

이를 위해 `@Configuration` 클래스는 WebMvcConfigurerAdapter 클래스를 재정의한다.

```java
@Configuration
public class WebMVCConfig extends WebMvcConfigurerAdapter {
    // ...
    @Override
    @Description("Custom Conversion Service")
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new NameFormatter());
    }
}
```

NameFormatter 클래스는 Spring Formatter 인터페이스를 구현한다.

`#conversions` 유틸리티를 사용하여 객체를 표시용으로 변환할 수도 있다. 유틸리티 함수의 구문은 `#conversions.convert(Object, Class)` 이며 여기서 Object는 Class 유형으로 변환된다.

소수 부분이 제거된 학생 개체 백분율 필드를 표시하는 방법은 다음과 같다.

```html
<tr th:each="student: ${students}">
    <td th:text="${#conversions.convert(student.percentage, 'Integer')}" />
</tr>
```

## [출처 및 참고]
* [https://www.baeldung.com/thymeleaf-in-spring-mvc](https://www.baeldung.com/thymeleaf-in-spring-mvc)
