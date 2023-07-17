---
title: Spring ModelAttribute
author: dejavuhyo
date: 2023-07-18 08:25:00 +0900
categories: [Framework, Spring]
tags: [spring-modelattribute, modelattribute, 스프링-modelattribute]
---

## 1. @ModelAttribute
`@ModelAttribute`를 메서드 매개변수로 사용하거나 메서드 수준에서 사용할 수 있다.

### 1) Method Level
메서드 수준에서 주석을 사용하면 메서드의 목적이 하나 이상의 모델 속성을 추가하는 것임을 나타낸다. 이러한 메서드는 `@RequestMapping` 메서드와 동일한 인수 유형을 지원 하지만 요청에 직접 매핑할 수는 없다.

작동 방식을 이해하기 위한 간단한 예이다.

```java
@ModelAttribute
public void addAttributes(Model model) {
    model.addAttribute("msg", "Welcome to the Netherlands!");
}
```

위의 예에서 컨트롤러 클래스에 정의된 모든 모델에 msg라는 속성을 추가하는 메서드를 볼 수 있다.

일반적으로 Spring MVC는 요청 핸들러 메서드를 호출하기 전에 항상 해당 메서드를 먼저 호출한다. 기본적으로 `@RequestMapping` 주석이 달린 컨트롤러 메서드가 호출되기 전에 `@ModelAttribute` 메서드가 호출된다. 이는 컨트롤러 메서드 내에서 처리가 시작되기 전에 모델 개체를 만들어야 하기 때문이다.

각 클래스에 `@ControllerAdvice`로 주석을 추가하는 것도 중요하다. 따라서 전역으로 식별되는 값을 모델에 추가할 수 있다. 이는 실제로 모든 요청에 ​​대해 응답의 모든 메서드에 대한 기본값이 존재함을 의미한다.

### 2) Method Argument
주석을 메서드 인수로 사용하면 모델에서 인수를 검색하도록 나타낸다. 주석이 없으면 먼저 인스턴스화한 다음 모델에 추가해야 한다. 모델에 있는 인수 필드는 이름이 일치하는 모든 요청 매개변수에서 채워져야 한다.

다음 코드 예제에서 addEmployee 끝점에 제출된 양식의 데이터로 직원 모델 특성을 채운다. Spring MVC는 제출 메소드를 호출하기 전에 배후에서 이 작업을 수행한다.

```java
@RequestMapping(value = "/addEmployee", method = RequestMethod.POST)
public String submit(@ModelAttribute("employee") Employee employee) {
    // Code that uses the employee object

    return "employeeView";
}
```

양식 데이터를 빈과 바인딩한다. `@RequestMapping` 주석이 달린 컨트롤러는 `@ModelAttribute` 주석이 달린 커스텀 클래스 인수를 가질 수 있다.

Spring MVC에서는 이를 데이터 바인딩이라고 한다. 각 양식 필드를 개별적으로 구문 분석하지 않아도 되는 공통 메커니즘이다.

## 2. Form 예시
사용자(특히 회사 직원)에게 일부 개인 정보(특히 이름 및 ID)를 입력하라는 메시지를 표시하는 매우 기본적인 양식인 개요 섹션에 설명된 예제를 확인한다. 제출이 완료되고 오류 없이 완료되면 사용자는 이전에 제출한 데이터가 다른 화면에 표시되기를 기대한다.

### 1) View
먼저 ID 및 이름 필드가 있는 간단한 양식이다.

```html
<form:form method="POST" action="/spring-mvc-basics/addEmployee" modelAttribute="employee">
    <form:label path="name">Name</form:label>
    <form:input path="name" />
    
    <form:label path="id">Id</form:label>
    <form:input path="id" />
    
    <input type="submit" value="Submit" />
</form:form>
```

### 2) Controller
view에 대한 논리를 구현할 컨트롤러 클래스이다.

```java
@Controller
@ControllerAdvice
public class EmployeeController {

    private Map<Long, Employee> employeeMap = new HashMap<>();

    @RequestMapping(value = "/addEmployee", method = RequestMethod.POST)
    public String submit(
      @ModelAttribute("employee") Employee employee,
      BindingResult result, ModelMap model) {
        if (result.hasErrors()) {
            return "error";
        }
        model.addAttribute("name", employee.getName());
        model.addAttribute("id", employee.getId());

        employeeMap.put(employee.getId(), employee);

        return "employeeView";
    }

    @ModelAttribute
    public void addAttributes(Model model) {
        model.addAttribute("msg", "Welcome to the Netherlands!");
    }
}
```

`submit()` 메소드에는 View에 바인딩된 Employee 객체가 있다. 양식 필드를 개체 모델에 간단하게 매핑할 수 있다. 메서드에서는 양식에서 값을 가져와서 ModelMap으로 설정한다.

결국 employeeView를 반환한다. 즉, 각 JSP 파일을 View 대표로 호출한다.

또한 `addAttributes()` 메서드도 있다. 그 목적은 전역적으로 식별될 모델에 값을 추가하는 것이다. 즉, 모든 컨트롤러 메서드에 대한 모든 요청은 기본값을 응답으로 반환한다. 또한 특정 클래스에 `@ControllerAdvice`로 주석을 달아야 한다.

### 3) Model
Model 개체는 매우 단순하며 "프론트 엔드" 특성에 필요한 모든 것을 포함한다.

```java
@XmlRootElement
public class Employee {

    private long id;
    private String name;

    public Employee(long id, String name) {
        this.id = id;
        this.name = name;
    }

    // standard getters and setters removed
}
```

### 4) 마무리
`@ControllerAdvice`는 컨트롤러, 특히 모든 `@RequestMapping` 메서드에 적용되는 `@ModelAttribute` 메서드를 지원한다. 물론 `addAttributes()` 메서드는 나머지 `@RequestMapping` 메서드보다 먼저 실행될 것이다.

이 점을 염두에 두고 `submit()` 및 `addAttributes()`가 모두 실행된 후 `${name}`과 같이 달러화된 중괄호 듀오 내에서 주어진 이름을 언급하여 Controller 클래스에서 반환된 view에서 참조할 수 있다.

### 5) 결과
양식에서 받은 내용을 인쇄한다.

```html
<h3>${msg}</h3>
Name : ${name}
ID : ${id}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-and-the-modelattribute-annotation](https://www.baeldung.com/spring-mvc-and-the-modelattribute-annotation)
