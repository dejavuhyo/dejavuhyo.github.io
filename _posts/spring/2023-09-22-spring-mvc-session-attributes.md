---
title: Spring MVC 세션 속성
author: dejavuhyo
date: 2023-09-22 07:30:00 +0900
categories: [Framework, Spring]
tags: [spring-session, session-attributes, 스프링-세션, 세션-속성]
---

## 1. 메이븐 설정
Spring Boot 스타터를 사용하여 프로젝트를 부트스트랩하고 필요한 모든 종속성을 가져온다.

설정에는 상위 선언, 웹 스타터 및 thymeleaf 스타터가 필요하다.

또한 단위 테스트에 몇 가지 추가 유틸리티를 제공하기 위해 스프링 테스트 스타터를 포함한다.

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <relativePath/>
</parent>
 
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
     </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

이러한 종속성의 최신 버전은 Maven Central에서 찾을 수 있다.

## 2. 사용 사례 예시
예에서는 간단한 "TODO" 애플리케이션을 구현한다. TodoItem 인스턴스를 생성하기 위한 양식과 모든 TodoItem을 표시하는 목록 보기가 있다.

양식을 사용하여 TodoItem을 생성하면 이후에 양식에 액세스할 때 가장 최근에 추가된 TodoItem의 값이 미리 채워진다. 우리는 이 기능을 사용하여 세션 범위에 저장된 양식 값을 "기억"하는 방법을 보여준다.

가지 모델 클래스는 간단한 POJO로 구현된다.

```java
public class TodoItem {

    private String description;
    private LocalDateTime createDate;

    // getters and setters
}
```

```java
public class TodoList extends ArrayDeque<TodoItem>{

}
```

TodoList 클래스는 ArrayDeque를 확장하여 peekLast 메소드를 통해 가장 최근에 추가된 항목에 대한 편리한 액세스를 제공한다.

2개의 컨트롤러 클래스가 필요하다. 미묘한 차이가 있지만 핵심 기능은 두 가지 모두에서 표현된다. 각각에는 3개의 `@RequestMapping`이 있다.

* `@GetMapping("/form")` - 이 메서드는 양식을 초기화하고 양식 보기를 렌더링하는 역할을 한다. TodoList가 비어 있지 않은 경우 메서드는 가장 최근에 추가된 TodoItem으로 양식을 미리 채운다.

* `@PostMapping("/form")` - 이 메소드는 제출된 TodoItem을 TodoList에 추가하고 목록 URL로 리디렉션하는 역할을 한다.

* `@GetMapping("/todos.html")` - 이 메소드는 표시를 위해 TodoList를 모델에 추가하고 목록 보기를 렌더링한다.

## 3. 범위가 지정된 프록시 사용

### 1) 설정
이 설정에서 TodoList는 프록시가 지원하는 세션 범위 `@Bean`으로 구성된다. `@Bean`이 프록시라는 사실은 이를 싱글톤 범위의 `@Controller`에 주입할 수 있다는 것을 의미한다.

컨텍스트가 초기화될 때 세션이 없기 때문에 Spring은 종속성으로 주입할 TodoList의 프록시를 생성한다. TodoList의 대상 인스턴스는 요청에 따라 필요에 따라 인스턴스화된다.

먼저 `@Configuration` 클래스 내에서 Bean을 정의한다.

```java
@Bean
@Scope(value = WebApplicationContext.SCOPE_SESSION, proxyMode = ScopedProxyMode.TARGET_CLASS)
public TodoList todos() {
    return new TodoList();
}
```

다음으로, `@Controller`에 대한 종속성으로 Bean을 선언 하고 다른 종속성과 마찬가지로 주입한다.

```java
@Controller
@RequestMapping("/scopedproxy")
public class TodoControllerWithScopedProxy {

    private TodoList todos;

    // constructor and request mappings
}
```

마지막으로 요청에서 빈을 사용하는 것은 단순히 메소드 호출을 포함한다.

```java
@GetMapping("/form")
public String showForm(Model model) {
    if (!todos.isEmpty()) {
        model.addAttribute("todo", todos.peekLast());
    } else {
        model.addAttribute("todo", new TodoItem());
    }
    return "scopedproxyform";
}
```

### 2) 단위 테스트
범위가 지정된 프록시를 사용하여 구현을 테스트하기 위해 먼저 SimpleThreadScope를 구성한다. 이렇게 하면 단위 테스트가 테스트 중인 코드의 런타임 조건을 정확하게 시뮬레이션할 수 있다.

먼저 TestConfig 및 CustomScopeConfigurer를 정의한다.

```java
@Configuration
public class TestConfig {

    @Bean
    public CustomScopeConfigurer customScopeConfigurer() {
        CustomScopeConfigurer configurer = new CustomScopeConfigurer();
        configurer.addScope("session", new SimpleThreadScope());
        return configurer;
    }
}
```

이제 양식의 초기 요청에 초기화되지 않은 TodoItem이 포함되어 있는지 테스트하는 것으로 시작할 수 있다.

```java
@RunWith(SpringRunner.class) 
@SpringBootTest
@AutoConfigureMockMvc
@Import(TestConfig.class) 
public class TodoControllerWithScopedProxyIntegrationTest {

    // ...

    @Test
    public void whenFirstRequest_thenContainsUnintializedTodo() throws Exception {
        MvcResult result = mockMvc.perform(get("/scopedproxy/form"))
          .andExpect(status().isOk())
          .andExpect(model().attributeExists("todo"))
          .andReturn();

        TodoItem item = (TodoItem) result.getModelAndView().getModel().get("todo");
 
        assertTrue(StringUtils.isEmpty(item.getDescription()));
    }
}
```

또한 제출이 리디렉션을 발행하고 후속 양식 요청이 새로 추가된 TodoItem으로 미리 채워져 있음을 확인할 수 있다.

```java
@Test
public void whenSubmit_thenSubsequentFormRequestContainsMostRecentTodo() throws Exception {
    mockMvc.perform(post("/scopedproxy/form")
      .param("description", "newtodo"))
      .andExpect(status().is3xxRedirection())
      .andReturn();

    MvcResult result = mockMvc.perform(get("/scopedproxy/form"))
      .andExpect(status().isOk())
      .andExpect(model().attributeExists("todo"))
      .andReturn();
    TodoItem item = (TodoItem) result.getModelAndView().getModel().get("todo");
 
    assertEquals("newtodo", item.getDescription());
}
```

### 3) 논의
범위가 지정된 프록시 전략을 사용하는 주요 특징은 요청 매핑 메서드 서명에 영향을 미치지 않는다는 것이다. 이는 `@SessionAttributes` 전략에 비해 매우 높은 수준의 가독성을 유지한다.

컨트롤러에는 기본적으로 싱글톤 범위가 있다는 점을 기억하면 도움이 될 수 있다.

이것이 단순히 프록시되지 않은 세션 범위의 Bean을 주입하는 대신 프록시를 사용해야 하는 이유이다. 더 작은 범위의 Bean을 더 큰 범위의 Bean에 주입할 수 없다.

이 경우 이를 시도하면 다음을 포함하는 메시지와 함께 예외가 트리거된다. *Scope 'session' is not active for the current thread.*

세션 범위로 컨트롤러를 정의하려는 경우 ProxyMode 지정을 피할 수 있다. 특히 각 사용자 세션마다 컨트롤러 인스턴스를 생성해야 하기 때문에 컨트롤러 생성 비용이 많이 드는 경우에는 단점이 있을 수 있다.

TodoList는 주입을 위해 다른 구성 요소에 사용할 수 있다. 이는 사용 사례에 따라 장점이 될 수도 있고 단점이 될 수도 있다. 전체 애플리케이션에서 Bean을 사용할 수 있게 만드는 것이 문제가 되는 경우 다음 예제에서 볼 수 있듯이 `@SessionAttributes`를 사용하는 대신 인스턴스의 범위를 컨트롤러로 지정할 수 있다.

## 4. @SessionAttributes 주석 사용

### 1) 설정
이 설정에서는 TodoList를 Spring 관리 `@Bean`으로 정의하지 않는다. 대신 이를 `@ModelAttribute`로 선언하고 `@SessionAttributes` 주석을 지정하여 컨트롤러의 세션으로 범위를 지정한다.

컨트롤러에 처음 액세스하면 Spring은 인스턴스를 인스턴스화하고 이를 Model에 배치 한다. `@SessionAttributes`에서도 빈을 선언하므로 Spring은 인스턴스를 저장한다.

먼저 컨트롤러에 메소드를 제공하여 Bean을 선언하고 `@ModelAttribute`로 메소드에 주석을 단다.

```java
@ModelAttribute("todos")
public TodoList todos() {
    return new TodoList();
}
```

다음으로 `@SessionAttributes`를 사용하여 TodoList를 세션 범위로 처리하도록 컨트롤러에 알린다.

```java
@Controller
@RequestMapping("/sessionattributes")
@SessionAttributes("todos")
public class TodoControllerWithSessionAttributes {
    // ... other methods
}
```

마지막으로 요청 내에서 빈을 사용하기 위해 `@RequestMapping`의 메소드 서명에 이에 대한 참조를 제공한다.

```java
@GetMapping("/form")
public String showForm(Model model, @ModelAttribute("todos") TodoList todos) {
 
    if (!todos.isEmpty()) {
        model.addAttribute("todo", todos.peekLast());
    } else {
        model.addAttribute("todo", new TodoItem());
    }
    return "sessionattributesform";
}
```

`@PostMapping` 메소드에서는 RedirectView를 반환하기 전에 RedirectAttributes를 주입 하고 addFlashAttribute를 호출한다. 이는 첫 번째 예와 비교하여 구현 시 중요한 차이점이다.

```java
@PostMapping("/form")
public RedirectView create(@ModelAttribute TodoItem todo, @ModelAttribute("todos") TodoList todos, RedirectAttributes attributes) {
    todo.setCreateDate(LocalDateTime.now());
    todos.add(todo);
    attributes.addFlashAttribute("todos", todos);
    return new RedirectView("/sessionattributes/todos.html");
}
```

Spring은 URL 매개변수의 인코딩을 지원하기 위해 리디렉션 시나리오를 위한 특수한 RedirectAttributes 구현 모델을 사용한다. 리디렉션 중에 모델에 저장된 모든 속성은 일반적으로 URL에 포함된 경우에만 프레임워크에서 사용할 수 있다.

addFlashAttribute를 사용함으로써 우리는 TodoList가 URL에서 인코딩할 필요 없이 리디렉션 후에도 유지되기를 원한다는 것을 프레임워크에 알리고 있다.

### 2) 단위 테스트
폼 뷰 컨트롤러 메서드의 단위 테스트는 첫 번째 예에서 살펴본 테스트와 동일하다. 그러나 `@PostMapping` 테스트는 동작을 확인하기 위해 플래시 속성에 액세스해야 하기 때문에 약간 다르다.

```java
@Test
public void whenTodoExists_thenSubsequentFormRequestContainsesMostRecentTodo() throws Exception {
    FlashMap flashMap = mockMvc.perform(post("/sessionattributes/form")
      .param("description", "newtodo"))
      .andExpect(status().is3xxRedirection())
      .andReturn().getFlashMap();

    MvcResult result = mockMvc.perform(get("/sessionattributes/form")
      .sessionAttrs(flashMap))
      .andExpect(status().isOk())
      .andExpect(model().attributeExists("todo"))
      .andReturn();
    TodoItem item = (TodoItem) result.getModelAndView().getModel().get("todo");
 
    assertEquals("newtodo", item.getDescription());
}
```

### 3) 논의
세션에 속성을 저장하기 위한 `@ModelAttribute` 및 `@SessionAttributes` 전략은 추가 컨텍스트 구성이나 Spring 관리 `@Bean`이 필요하지 않은 간단한 솔루션이다.

첫 번째 예와 달리 `@RequestMapping` 메소드에 TodoList를 삽입해야 한다.

또한 리디렉션 시나리오에는 플래시 속성을 활용해야 한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-session-attributes](https://www.baeldung.com/spring-mvc-session-attributes)
