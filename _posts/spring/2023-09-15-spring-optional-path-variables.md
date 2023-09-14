---
title: Spring 선택적 경로 변수
author: dejavuhyo
date: 2023-09-15 08:55:00 +0900
categories: [Framework, Spring]
tags: [spring-path, path-variables, optional-path, 스프링-경로-변수, 스프링-변수, 경로-변수]
---

## 1. @PathVariable 매개변수를 바인딩하는 방법
기본적으로 Spring은 처리기 메서드에서 `@PathVariable`로 주석이 달린 모든 매개변수를 URI 템플릿의 해당 변수와 바인딩하려고 시도한다. Spring이 실패하면 해당 핸들러 메소드에 요청을 전달하지 않는다.

예를 들어, id 경로 변수를 선택 사항으로 만들려고 시도하는(실패한) 다음 getArticle 메서드를 참고한다.

```java
@RequestMapping(value = {"/article/{id}"})
public Article getArticle(@PathVariable(name = "id") Integer articleId) {
    if (articleId != null) {
        //...
    } else {
        //...
    }
}
```

여기서 getArticle 메소드는 `/article` 및 `/article/{id}` 모두에 대한 요청을 처리해야 한다. Spring은 itemId 매개변수를 id 경로 변수(있는 경우)에 바인딩하려고 시도한다.

예를 들어 `/article/123`에 요청을 보내면 ArticleId 값이 123으로 설정된다.

반면 `/article`에 요청을 보내면 Spring은 다음 예외로 인해 상태 코드 500을 반환한다.

```text
org.springframework.web.bind.MissingPathVariableException:
  Missing URI template variable 'id' for method parameter of type Integer
```

이는 id가 누락되어 Spring이 articleId 매개변수에 대한 값을 설정할 수 없었기 때문이다.

따라서 해당 경로 변수가 없는 경우 특정 `@PathVariable` 매개변수 바인딩을 무시하도록 Spring에 지시할 수 있는 방법이 필요하다.

## 2. 경로 변수를 선택적으로 만들기

### 1) @PathVariable의 required 속성 사용
Spring 4.3.3부터 `@PathVariable` 주석은 경로 변수가 핸들러 메소드에 required인지 표시하는데 필요한 boolean 속성을 정의한다.

예를 들어, 다음 버전의 getArticle은 필수 속성을 사용한다.

```java
@RequestMapping(value = {"/article", "/article/{id}"})
public Article getArticle(@PathVariable(required = false) Integer articleId) {
   if (articleId != null) {
       //...
   } else {
       //...
   }
}
```

required 속성이 false 이므로 id 경로 변수가 요청에 전송되지 않아도 Spring은 불평하지 않는다. 즉, Spring은 ArticleId가 전송되면 id로 설정하고, 그렇지 않으면 null로 설정한다.

반면에 require가 true인 경우 id가 누락된 경우 Spring은 예외를 발생시킨다.

### 2) Optional 매개변수 유형 사용
다음 구현은 [JDK 8의 Optional 클래스](https://www.baeldung.com/java-optional)와 함께 Spring 4.1이 어떻게 documentId를 선택적으로 만드는 또 다른 방법을 제공하는지 보여준다.

```java
@RequestMapping(value = {"/article", "/article/{id}"}")
public Article getArticle(@PathVariable Optional<Integer> optionalArticleId) {
    if (optionalArticleId.isPresent()) {
        Integer articleId = optionalArticleId.get();
        //...
    } else {
        //...
    }
}
```

여기서 Spring은 id 값을 보유하기 위해 `Optional<Integer>` 인스턴스인 optionArticleId를 생성한다. id가 있으면 optionArticleId는 해당 값을 래핑하고, 그렇지 않으면 optionArticleId는 null 값을 래핑한다. 그런 다음 Optional의 `isPresent()`, `get()` 또는 `orElse()` 메서드를 사용하여 값을 작업할 수 있다.

### 3) Map 매개변수 유형 사용
Spring 3.2부터 `@PathVariable` 매개변수에 대한 Map를 사용하여 사용할 수 있는 선택적 경로 변수를 정의하는 또 다른 방법은 다음과 같다.

```java
@RequestMapping(value = {"/article", "/article/{id}"})
public Article getArticle(@PathVariable Map<String, String> pathVarsMap) {
    String articleId = pathVarsMap.get("id");
    if (articleId != null) {
        Integer articleIdAsInt = Integer.valueOf(articleId);
        //...
    } else {
        //...
    }
}
```

이 예에서 `Map<String, String> pathVarsMap` 매개 변수는 URI에 있는 모든 경로 변수를 key/value 쌍으로 수집한다. 그런 다음 `get()` 메서드를 사용하여 특정 경로 변수를 가져올 수 있다.

Spring은 경로 변수의 값을 String으로 추출하기 때문에 `Integer.valueOf()` 메서드를 사용하여 이를 Integer로 변환했다.

### 4) 두 가지 Handler Methods 사용
레거시 Spring 버전을 사용하는 경우 getArticle 핸들러 메서드를 두 가지 메서드로 나눌 수 있다.

첫 번째 방법은 `/article/{id}`에 대한 요청을 처리한다.

```java
@RequestMapping(value = "/article/{id}")
public Article getArticle(@PathVariable(name = "id") Integer articleId) {
    //...
}
```

두 번째 방법은 `/article`에 대한 요청을 처리한다.

```java
@RequestMapping(value = "/article")
public Article getDefaultArticle() {
    //...
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-optional-path-variables](https://www.baeldung.com/spring-optional-path-variables)
