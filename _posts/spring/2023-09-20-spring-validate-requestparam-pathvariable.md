---
title: Spring RequestParams 및 PathVariables 유효성 검사
author: dejavuhyo
date: 2023-09-20 08:15:00 +0900
categories: [Framework, Spring]
tags: [spring-validate, validate-requestparam, validate-pathvariable, 스프링-유효성, requestparams-유효성, pathvariable-유효성]
---

## 1. 구성
Java Validation API를 사용하려면 [hibernate-validator](https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator)와 같은 JSR 303 구현을 추가해야 한다.

```xml
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.0.10.Final</version>
</dependency>
```

또한 `@Validated` 주석을 추가하여 컨트롤러의 요청 매개변수와 경로 변수 모두에 대한 유효성 검사를 활성화해야 한다.

```java
@RestController
@RequestMapping("/")
@Validated
public class Controller {
    // ...
}
```

매개변수 유효성 검증을 활성화하려면 MethodValidationPostProcessor 빈도 필요하다. Spring Boot 애플리케이션을 사용하는 경우 클래스 경로에 hibernate-validator 종속성이 있으므로 이 빈은 자동으로 구성된다.

그렇지 않으면 표준 Spring 애플리케이션에서 이 Bean을 명시적으로 추가해야 한다.

```java
@EnableWebMvc
@Configuration
@ComponentScan("com.baeldung.spring")
public class ClientWebConfigJava implements WebMvcConfigurer {
    @Bean
    public MethodValidationPostProcessor methodValidationPostProcessor() {
        return new MethodValidationPostProcessor();
    }
    // ...
}
```

기본적으로 Spring에서 경로 또는 요청 유효성 검사 중 오류가 발생하면 HTTP 500 응답이 발생한다. 예에서는 ControllerAdvice의 사용자 정의 구현을 사용하여 이러한 종류의 오류를 더 읽기 쉬운 방식으로 처리하고 잘못된 요청에 대해 HTTP 400을 반환한다.

## 2. RequestParam 유효성 검사
숫자로 된 요일을 컨트롤러 메소드에 요청 매개변수로 전달하는 예이다.

```java
@GetMapping("/name-for-day")
public String getNameOfDayByNumber(@RequestParam Integer dayOfWeek) {
    // ...
}
```

목표는 dayOfWeek의 값이 1에서 7 사이인지 확인하는 것이다. 이를 위해 `@Min` 및 `@Max` 주석을 사용한다.

```java
@GetMapping("/name-for-day")
public String getNameOfDayByNumber(@RequestParam @Min(1) @Max(7) Integer dayOfWeek) {
    // ...
}
```

이러한 조건과 일치하지 않는 요청은 기본 오류 메시지와 함께 HTTP 상태 400을 반환한다.

예를 들어 `http:// localhost:8080/name-for-day?dayOfWeek=24`를 호출하면 응답 메시지는 다음과 같다.

```text
getNameOfDayByNumber.dayOfWeek: must be less than or equal to 7
```

사용자 정의 메시지를 추가하여 기본 메시지를 변경할 수 있다.

```text
@Max(value = 1, message = "day number has to be less than or equal to 7")
```

## 3. PathVariable 유효성 검사
`@RequestParam`과 마찬가지로 `javax.validation.constraints` 패키지의 주석을 사용하여 `@PathVariable`의 유효성을 검사 할 수 있다.

문자열 매개변수가 비어 있지 않고 길이가 10보다 작거나 같은지 확인하는 예이다.

```java
@GetMapping("/valid-name/{name}")
public void createUsername(@PathVariable("name") @NotBlank @Size(max = 10) String username) {
    // ...
}
```

예를 들어, 이름 매개변수가 10자보다 긴 요청은 다음 메시지와 함께 HTTP 400 오류가 발생한다.

```text
createUser.name:size must be between 0 and 10
```

`@Size` 주석에 메시지 매개변수를 설정하면 기본 메시지를 쉽게 덮어쓸 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-validate-requestparam-pathvariable](https://www.baeldung.com/spring-validate-requestparam-pathvariable)
