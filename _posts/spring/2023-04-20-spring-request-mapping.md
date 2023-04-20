---
title: Spring Request Mapping
author: dejavuhyo
date: 2023-04-20 09:20:00 +0900
categories: [Framework, Spring]
tags: [spring-request-mapping, request-mapping, uri-patterns, pathvariable, 스프링-리퀘스트-매핑, 리퀘스트-매핑]
---

## 1. Request Mapping
`@RequestMapping` 주석을 사용하여 요청을 컨트롤러 메서드에 매핑할 수 있다. URL, HTTP 메서드, 요청 매개 변수, 헤더 및 미디어 유형별로 일치하는 다양한 속성이 있다. 클래스 수준에서 공유 매핑을 표현하거나 메서드 수준에서 특정 엔드포인트 매핑으로 범위를 좁힐 수 있다.

`@RequestMapping`의 HTTP 메서드별 바로 가기 변형이다.

* `@GetMapping`

* `@PostMapping`

* `@PutMapping`

* `@DeleteMapping`

* `@PatchMapping`

대부분의 컨트롤러 메소드는 기본적으로 모든 HTTP 메소드와 일치하는 `@RequestMapping`을 사용하는 것과 비교하여 특정 HTTP 메소드에 매핑되어야 하기 때문에 바로 가기는 제공되는 사용자 지정 주석이다. 공유 매핑을 표현하려면 클래스 수준에서 `@RequestMapping`이 여전히 필요하다.

```java
@RestController
@RequestMapping("/persons")
class PersonController {

    @GetMapping("/{id}")
    public Person getPerson(@PathVariable Long id) {
        // ...
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public void add(@RequestBody Person person) {
        // ...
    }
}
```

## 2. URI patterns
`@RequestMapping` 메서드는 URL 패턴을 사용하여 매핑할 수 있다.

* PathPattern: URL 경로와 일치하는 사전 구문 분석된 패턴으로, PathContainer로도 사전 구문 분석된다. 웹 사용을 위해 설계된 이 솔루션은 인코딩 및 경로 매개 변수를 효과적으로 처리하고 효율적으로 일치시킨다.

* AntPathMatcher: 문자열 패턴을 문자열 경로와 일치시킨다. 이 솔루션은 Spring 구성에서도 클래스 경로, 파일 시스템 및 기타 위치의 리소스를 선택하는데 사용된다. 효율성이 떨어지고 문자열 경로 입력은 인코딩 및 기타 URL 문제를 효과적으로 처리하는데 어려움이 있다.

PathPattern은 웹 애플리케이션에 권장되는 솔루션이며 Spring WebFlux에서 유일하게 선택할 수 있다. 버전 5.3부터 Spring MVC에서 사용할 수 있도록 활성화되었으며 버전 6.0부터는 기본적으로 활성화되어 있다.

PathPattern은 AntPathMatcher와 동일한 패턴 구문을 지원한다. 또한 경로 끝에서 0개 이상의 경로 세그먼트를 일치시키는 캡처 패턴(예: `{*spring}`)도 지원한다. 또한 PathPattern은 패턴 끝에만 허용되도록 여러 경로 세그먼트를 일치시키는 `**`의 사용을 제한한다. 이를 통해 주어진 요청에 가장 적합한 패턴을 선택할 때 모호성이 발생하는 많은 경우가 제거된다.

* `"/resources/ima?e.png"`: 경로 세그먼트에서 하나의 문자와 일치

* `"/resources/*.png"`: 경로 세그먼트에서 0자 이상 일치

* `"/resources/**"`: 여러 경로 세그먼트와 일치

* `"/projects/{project}/versions"`: 경로 세그먼트를 일치시키고 변수로 캡처

* `"/projects/{project:[a-z]+}/versions"`: 정규식과 변수 일치 및 캡처

캡처된 URI 변수는 `@PathVariable`을 사용하여 액세스할 수 있다.

```java
@GetMapping("/owners/{ownerId}/pets/{petId}")
public Pet findPet(@PathVariable Long ownerId, @PathVariable Long petId) {
    // ...
}
```

다음 예제와 같이 클래스 및 메서드 수준에서 URI 변수를 선언할 수 있다.

```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class OwnerController {

    @GetMapping("/pets/{petId}")
    public Pet findPet(@PathVariable Long ownerId, @PathVariable Long petId) {
        // ...
    }
}
```

URI 변수가 해당 유형으로 자동 변환되거나 `TypeMismatchException`이 발생한다. 기본적으로 단순 유형(int, long, Date 등)이 지원되며 다른 데이터 유형에 대한 지원을 등록할 수 있다.

URI 변수의 이름을 명시적으로 지정할 수 있지만(예: `@PathVariable("customId")`), 이름이 동일하고 코드가 `-parameters` 컴파일러 플래그로 컴파일된 경우에는 해당 세부 정보를 생략할 수 있다.

```java
@GetMapping("/{name:[a-z-]+}-{version:\\d\\.\\d\\.\\d}{ext:\\.[a-z]+}")
public void handle(@PathVariable String name, @PathVariable String version, @PathVariable String ext) {
    // ...
}
```

URI 경로 패턴에는 로컬, 시스템, 환경 및 기타 속성 소스에 대해 `PropertySourcesPlaceholderConfigurer`를 사용하여 시작 시 확인되는 `${…}` 자리 표시자가 포함될 수도 있다. 예를 들어 이를 사용하여 일부 외부 구성을 기반으로 기본 URL을 매개 변수화할 수 있다.

## [출처 및 참고]
* [https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-requestmapping](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-requestmapping)
