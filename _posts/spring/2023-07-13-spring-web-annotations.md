---
title: Spring Web Annotations
author: dejavuhyo
date: 2023-07-23 08:40:00 +0900
categories: [Framework, Spring]
tags: [spring-web, spring-annotations, annotations, 스프링-웹, 스프링-어노테이션, 어노테이션]
---

## 1. @RequestMapping
`@RequestMapping`은 `@Controller` 클래스 내부에 request handler methods를 표시한다. 다음을 사용하여 구성할 수 있다.

* path 또는 해당 별칭, 이름, 그리고 값: 메서드가 매핑되는 URL

* method: 호환되는 HTTP 방법

* params: HTTP 매개변수의 존재, 부재 또는 값을 기반으로 요청을 필터링

* headers: HTTP 헤더의 존재, 부재 또는 값을 기반으로 요청을 필터링

* consumes: 메서드가 HTTP 요청 본문에서 소비할 수 있는 미디어 유형

* produces: 서드가 HTTP 응답 본문에서 생성할 수 있는 미디어 유형

간단한 예이다.

```java
@Controller
class VehicleController {

    @RequestMapping(value = "/vehicles/home", method = RequestMethod.GET)
    String home() {
        return "home";
    }
}
```

클래스 수준에서 이 주석을 적용하면 `@Controller` 클래스의 모든 핸들러 메서드에 대한 기본 설정을 제공할 수 있다. 유일한 예외는 Spring이 메서드 수준 설정으로 재정의하지 않고 두 경로 부분을 추가하는 URL이다.

예를 들어 다음 구성은 위의 구성과 동일한 효과를 가진다.

```java
@Controller
@RequestMapping(value = "/vehicles", method = RequestMethod.GET)
class VehicleController {

    @RequestMapping("/home")
    String home() {
        return "home";
    }
}
```

또한 `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping` 및 `@PatchMapping`은 각각 GET, POST, PUT, DELETE 및 PATCH로 설정된 HTTP 메서드를 사용하는 `@RequestMapping`의 다른 변형이다.

이들은 `Spring 4.3` 릴리스부터 사용할 수 있다.

## 2. @RequestBody
HTTP 요청의 본문을 객체에 매핑하는 `@RequestBody`이다.

```java
@PostMapping("/save")
void saveVehicle(@RequestBody Vehicle vehicle) {
    // ...
}
```

역직렬화는 자동이며 요청의 콘텐츠 유형에 따라 다르다.

## 3. @PathVariable
이 주석은 메서드 인수가 URI 템플릿 변수에 바인딩되어 있음을 나타낸다. `@RequestMapping` 주석을 사용하여 URI 템플릿을 지정 하고 `@PathVariable`을 사용하여 템플릿 부분 중 하나에 메서드 인수를 바인딩 할 수 있다.

이름 또는 별칭인 value 인수를 사용하여 이를 달성할 수 있다.

```java
@RequestMapping("/{id}")
Vehicle getVehicle(@PathVariable("id") long id) {
    // ...
}
```

템플릿의 파트 이름이 메서드 인수의 이름과 일치하면 주석에 지정할 필요가 없다.

```java
@RequestMapping("/{id}")
Vehicle getVehicle(@PathVariable long id) {
    // ...
}
```

또한 필수 인수를 false로 설정하여 경로 변수를 선택 사항으로 표시할 수 있다.

```java
@RequestMapping("/{id}")
Vehicle getVehicle(@PathVariable(required = false) long id) {
    // ...
}
```

## 4. @RequestParam
HTTP 요청 매개변수에 액세스하기 위해 `@RequestParam`을 사용한다.

```java
@RequestMapping
Vehicle getVehicleByParam(@RequestParam("id") long id) {
    // ...
}
```

`@PathVariable` 주석과 동일한 구성 옵션이 있다.

이러한 설정 외에도 `@RequestParam`을 사용하면 Spring이 요청에서 값이 없거나 비어 있는 경우 주입된 값을 지정할 수 있다. 이를 달성하려면 defaultValue 인수를 설정해야 한다.

기본값을 제공하면 암시적으로 required가 false 로 설정된다.

```java
@RequestMapping("/buy")
Car buyCar(@RequestParam(defaultValue = "5") int seatCount) {
    // ...
}
```

매개변수 외에도 액세스할 수 있는 다른 HTTP 요청 부분인 쿠키 및 헤더가 있다. 각각 `@CookieValue` 및 `@RequestHeader` 주석으로 액세스할 수 있다.

`@RequestParam`과 같은 방식으로 설정할 수 있다.

## 5. 응답 처리 Annotations
Spring MVC에서 HTTP 응답을 조작하는 가장 일반적인 주석이다.

### 1) @ResponseBody
`@ResponseBody`로 요청 처리기 메서드를 표시하면 Spring은 메서드의 결과를 응답 자체로 처리한다.

```java
@ResponseBody
@RequestMapping("/hello")
String hello() {
    return "Hello World!";
}
```

이 주석으로 `@Controller` 클래스에 주석을 달면 모든 요청 처리기 메서드가 이를 사용한다.

### 2) @ExceptionHandler
이 주석을 사용하여 사용자 지정 오류 처리기 메서드를 선언할 수 있다 . Spring은 요청 처리기 메서드가 지정된 예외를 throw할 때 이 메서드를 호출한다.

잡힌 예외는 메서드에 인수로 전달할 수 있다.

```java
@ExceptionHandler(IllegalArgumentException.class)
void onIllegalArgumentException(IllegalArgumentException exception) {
    // ...
}
```

### 3) @ResponseStatus
요청 처리기 메서드에 이 주석을 추가하면 응답의 원하는 HTTP 상태를 지정할 수 있다. code 인수 또는 별칭인 value 인수를 사용하여 상태 코드를 선언할 수 있다.

또한 reason 인수를 사용하여 이유를 제공할 수 있다.

`@ExceptionHandler`와 함께 사용할 수도 있다.

```java
@ExceptionHandler(IllegalArgumentException.class)
@ResponseStatus(HttpStatus.BAD_REQUEST)
void onIllegalArgumentException(IllegalArgumentException exception) {
    // ...
}
```

## 6. 기타 웹 Annotations
일부 주석은 HTTP 요청 또는 응답을 직접 관리하지 않는다.

### 1) @Controller
`@Controller`로 Spring MVC 컨트롤러를 정의할 수 있다. 자세한 내용은 [Spring Bean Annotations](https://www.baeldung.com/spring-bean-annotations)을 참조한다.

### 2) @RestController
`@RestController`는 `@Controller`와 `@ResponseBody`를 결합한다.

따라서 다음 선언은 동일하다.

```java
@Controller
@ResponseBody
class VehicleRestController {
    // ...
}
```

```java
@RestController
class VehicleRestController {
    // ...
}
```

### 3) @ModelAttribute
이 주석을 사용하면 모델 키를 제공하여 MVC `@Controller`의 모델에 이미 있는 요소에 액세스 할 수 있다.

```java
@PostMapping("/assemble")
void assembleVehicle(@ModelAttribute("vehicle") Vehicle vehicleInModel) {
    // ...
}
```

`@PathVariable` 및 `@RequestParam`과 마찬가지로 인수의 이름이 동일한 경우 모델 키를 지정할 필요가 없다.

```java
@PostMapping("/assemble")
void assembleVehicle(@ModelAttribute Vehicle vehicle) {
    // ...
}
```

게다가 `@ModelAttribute`는 또 다른 용도가 있다. 메서드에 주석을 추가하면 Spring은 자동으로 메서드의 반환 값을 모델에 추가한다.

```java
@ModelAttribute("vehicle")
Vehicle getVehicle() {
    // ...
}
```

이전과 마찬가지로 모델 키를 지정할 필요가 없으며 Spring은 기본적으로 메서드 이름을 사용한다.

```java
@ModelAttribute
Vehicle vehicle() {
    // ...
}
```

Spring은 요청 처리기 메서드를 호출하기 전에 클래스의 모든 `@ModelAttribute` 주석 메서드를 호출한다.

`@ModelAttribute`에 대한 자세한 내용은 [여기](https://www.baeldung.com/spring-mvc-and-the-modelattribute-annotation)에서 확인할 수 있다.

### 4) @CrossOrigin
`@CrossOrigin`은 주석이 달린 요청 처리기 메서드에 대한 도메인 간 통신을 활성화한다.

```java
@CrossOrigin
@RequestMapping("/hello")
String hello() {
    return "Hello World!";
}
```

클래스를 표시하면 그 안에 있는 모든 요청 처리기 메서드에 적용된다.

이 주석의 인수로 CORS 동작을 미세 조정할 수 있다.

자세한 내용은 [여기](https://www.baeldung.com/spring-cors)를 참조한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-annotations](https://www.baeldung.com/spring-mvc-annotations)
