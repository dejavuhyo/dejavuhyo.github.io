---
title: Spring Controller 및 RestController
author: dejavuhyo
date: 2023-07-19 08:55:00 +0900
categories: [Framework, Spring]
tags: [spring-controller, spring-restcontroller, controller, restcontroller, 스프링-컨트롤러, 스프링-레스트컨트롤러]
---

## 1. Spring MVC @Controller
`@Controller` 주석으로 클래식 컨트롤러에 주석을 달 수 있다. 이것은 클래스 경로 스캐닝을 통해 구현 클래스를 자동 감지할 수 있게 해주는 `@Component` 클래스의 단순한 특수화이다.

일반적으로 요청 처리 방법을 위해 `@RequestMapping` 주석과 함께 `@Controller`를 사용한다.

Spring MVC 컨트롤러의 간단한 예이다.

```java
@Controller
@RequestMapping("books")
public class SimpleBookController {

    @GetMapping("/{id}", produces = "application/json")
    public @ResponseBody Book getBook(@PathVariable int id) {
        return findBookById(id);
    }

    private Book findBookById(int id) {
        // ...
    }
}
```

`@ResponseBody`로 요청 처리 방법에 주석을 달았다. 이 주석을 사용하면 반환 객체를 HttpResponse로 자동 직렬화할 수 있다.

## 2. Spring MVC @RestController
`@RestController`는 컨트롤러의 특수 버전이다. 여기에는 `@Controller` 및 `@ResponseBody` 주석이 포함되어 있으며 결과적으로 컨트롤러 구현을 단순화한다.

```java
@RestController
@RequestMapping("books-rest")
public class SimpleBookRestController {
    
    @GetMapping("/{id}", produces = "application/json")
    public Book getBook(@PathVariable int id) {
        return findBookById(id);
    }

    private Book findBookById(int id) {
        // ...
    }
}
```

컨트롤러에는 `@RestController` 주석이 추가된다. 따라서 `@ResponseBody`는 필요하지 않다.

컨트롤러 클래스의 모든 요청 처리 메서드는 자동으로 반환 객체를 HttpResponse로 직렬화한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-controller-vs-restcontroller](https://www.baeldung.com/spring-controller-vs-restcontroller)
