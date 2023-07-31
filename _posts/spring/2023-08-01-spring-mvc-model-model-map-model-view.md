---
title: Spring MVC Model, ModelMap 및 ModelAndView
author: dejavuhyo
date: 2023-08-01 08:50:00 +0900
categories: [Framework, Spring]
tags: [spring-mvc, model, model-map, model-view]
---

## 1. 메이븐 종속성
`pom.xml` 파일에서 spring-boot-starter-web 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>3.0.2</version>
</dependency>
```

최신 버전의 spring-boot-starter-web 종속성은 [여기](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web)에서 찾을 수 있다.

그리고 Thymeleaf를 보기로 사용하는 경우 이 종속성을 `pom.xml`에 추가해야 한다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
    <version>3.0.2</version>
</dependency>
```

최신 버전의 Thymeleaf 종속성은 [여기](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-thymeleaf)에서 찾을 수 있다.

## 2. Model
모델은 뷰를 렌더링하는데 사용되는 속성을 제공할 수 있다.

사용 가능한 데이터가 있는 뷰를 제공하기 위해 이 데이터를 Model 개체에 추가하기만 하면 된다. 또한 속성이 있는 맵을 모델 인스턴스와 병합할 수 있다.

```java
@GetMapping("/showViewPage")
public String passParametersWithModel(Model model) {
    Map<String, String> map = new HashMap<>();
    map.put("spring", "mvc");
    model.addAttribute("message", "Baeldung");
    model.mergeAttributes(map);
    return "view/viewPage";
}
```

## 3. ModelMap
Model 인터페이스와 마찬가지로 ModelMap은 뷰를 렌더링하기 위해 값을 전달하는 데에도 사용된다.

ModelMap의 장점은 값 모음을 전달하고 이러한 값을 Map 내에 있는 것처럼 처리할 수 있는 기능을 제공한다는 것이다.

```java
@GetMapping("/printViewPage")
public String passParametersWithModelMap(ModelMap map) {
    map.addAttribute("welcomeMessage", "welcome");
    map.addAttribute("message", "Baeldung");
    return "view/viewPage";
}
```

## 4. ModelAndView
뷰에 값을 전달하는 최종 인터페이스는 ModelAndView 이다.

이 인터페이스를 사용하면 Spring MVC가 요구하는 모든 정보를 한 번에 전달할 수 있다.

```java
@GetMapping("/goToViewPage")
public ModelAndView passParametersWithModelAndView() {
    ModelAndView modelAndView = new ModelAndView("view/viewPage");
    modelAndView.addObject("message", "Baeldung");
    return modelAndView;
}
```

## 5. View
이러한 모델 내에 배치하는 모든 데이터는 뷰(일반적으로 웹 페이지를 렌더링하는 템플릿 뷰)에서 사용된다.

컨트롤러의 메서드가 보기로 지정하는 Thymeleaf 템플릿 파일이 있는 경우, 모델을 통해 전달된 매개변수는 thymeleaf HTML 코드 내에서 액세스할 수 있다.

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Title</title>
</head>
<body>
    <div>Web Application. Passed parameter : <span th:text="${message}"></span></div>
</body>
</html>
```

여기에 전달된 매개변수는 자리 표시자로 알려진 `${message}` 구문을 통해 사용된다. Thymeleaf 템플릿 엔진은 자리 표시자를 모델을 통해 전달된 동일한 이름의 속성의 실제 값으로 대체한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-model-model-map-model-view](https://www.baeldung.com/spring-mvc-model-model-map-model-view)
