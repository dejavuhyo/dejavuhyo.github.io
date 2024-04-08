---
title: Spring 5 WebFlux
author: dejavuhyo
date: 2024-04-08 09:52:00 +0900
categories: [Framework, Spring]
tags: [spring-webflux, webflux, webclient, rest]
---

## 1. Spring WebFlux Framework
Spring WebFlux는 내부적으로 [Project Reactor](https://projectreactor.io/)와 해당 게시자 구현인 [Flux](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html) 및 [Mono](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html)를 사용한다.

새로운 프레임워크는 두 가지 프로그래밍 모델을 지원한다.

* Annotation-based 반응형 구성 요소

* 기능적 라우팅 및 처리

## 2. 종속성
다른 모든 필수 종속성을 가져오는 `spring-boot-starter-webflux` 종속성을 추가한다.

* 기본 Spring Boot 애플리케이션 설정을 위한 `spring-boot` 및 `spring-boot-starter`

* `spring-webflux` 프레임워크

* 반응성 스트림과 reactor-netty에 필요한 reactor-core

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
    <version>3.1.2</version>
</dependency>
```

최신 [spring-boot-starter-webflux](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-webflux)는 Maven Central에서 다운로드할 수 있다.

## 3. Reactive REST 애플리케이션
이제 Spring WebFlux를 사용하여 매우 간단한 반응형 REST EmployeeManagement 애플리케이션을 구축한다.

* 간단한 도메인 모델 사용 - id와 name 필드가 있는 직원

* RestController를 사용하여 REST API를 구축하여 직원 리소스를 단일 리소스 및 컬렉션으로 게시

* 동일한 리소스를 검색하기 위해 WebClient로 클라이언트 구축

* WebFlux 및 Spring Security를 ​​사용하여 안전한 반응 엔드포인트 생성

## 4. Reactive RestController
Spring WebFlux는 Spring Web MVC 프레임워크와 동일한 방식으로 주석 기반 구성을 지원한다.

우선 서버에서 Employee 리소스의 반응 스트림을 게시하는 주석이 달린 컨트롤러를 만든다.

주석이 달린 EmployeeController를 만든다.

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    private final EmployeeRepository employeeRepository;
    
    // constructor...
}
```

EmployeeRepository는 non-blocking reactive streams을 지원하는 모든 데이터 저장소가 될 수 있다.

### 1) Single Resource
그런 다음 단일 Employee 리소스를 게시하는 컨트롤러에서 엔드포인트를 생성한다.

```java
@GetMapping("/{id}")
public Mono<Employee> getEmployeeById(@PathVariable String id) {
    return employeeRepository.findEmployeeById(id);
}
```

최대 한 명의 직원을 반환하므로 단일 Employee 리소스를 Mono로 래핑한다.

### 2) Collection Resource
또한 모든 Employees의 컬렉션 리소스를 게시하는 엔드포인트를 추가한다.

```java
@GetMapping
public Flux<Employee> getAllEmployees() {
    return employeeRepository.findAllEmployees();
}
```

컬렉션 리소스의 경우 Employee 유형의 Flux를 사용한다. 이는 `0..n` elements의 게시자이기 때문이다.

## 5. Reactive Web Client
Spring 5에 도입된 [WebClient](https://docs.spring.io/spring-framework/reference/web/webflux-webclient.html)는 반응형 스트림을 지원하는 비차단 클라이언트이다.

WebClient를 사용하여 EmployeeController가 제공하는 엔드포인트에서 데이터를 검색하는 클라이언트를 생성할 수 있다.

간단한 EmployeeWebClient를 만든다.

```java
public class EmployeeWebClient {

    WebClient client = WebClient.create("http://localhost:8080");

    // ...
}
```

여기에서는 create 팩토리 메소드를 사용하여 WebClient를 생성했다. `localhost:8080`을 가리키므로 이 클라이언트 인스턴스에서 수행한 호출에 상대 URL을 사용할 수 있다.

### 1) 단일 리소스 검색
`/employee/{id}` 엔드포인트에서 Mono 유형의 단일 리소스를 검색하려면 다음을 수행한다.

```java
Mono<Employee> employeeMono = client.get()
  .uri("/employees/{id}", "1")
  .retrieve()
  .bodyToMono(Employee.class);

employeeMono.subscribe(System.out::println);
```

### 2) 컬렉션 리소스 검색
마찬가지로 엔드포인트 `/employees`에서 Flux 유형의 컬렉션 리소스를 검색하려면 다음을 수행한다.

```java
Flux<Employee> employeeFlux = client.get()
  .uri("/employees")
  .retrieve()
  .bodyToFlux(Employee.class);

employeeFlux.subscribe(System.out::println);
```

## 6. Spring WebFlux Security
Spring Security를 ​​사용하여 반응 엔드포인트를 보호할 수 있다.

EmployeeController에 새로운 엔드포인트가 있다고 가정한다. 이 엔드포인트는 직원 세부 정보를 업데이트하고 업데이트된 직원을 다시 보낸다.

이를 통해 사용자는 기존 직원을 변경할 수 있으므로 이 엔드포인트를 ADMIN 역할 사용자로만 제한하려고 한다.

결과적으로 EmployeeController에 새 메서드를 추가한다.

```java
@PostMapping("/update")
public Mono<Employee> updateEmployee(@RequestBody Employee employee) {
    return employeeRepository.updateEmployee(employee);
}
```

이제 이 방법에 대한 액세스를 제한하기 위해 SecurityConfig를 만들고 ADMIN 사용자만 허용하는 경로 기반 규칙을 정의한다.

```java
@EnableWebFluxSecurity
public class EmployeeWebSecurityConfig {

    // ...

    @Bean
    public SecurityWebFilterChain springSecurityFilterChain(
      ServerHttpSecurity http) {
        http.csrf().disable()
          .authorizeExchange()
          .pathMatchers(HttpMethod.POST, "/employees/update").hasRole("ADMIN")
          .pathMatchers("/**").permitAll()
          .and()
          .httpBasic();
        return http.build();
    }
}
```

이 구성은 `/employees/update` 엔드포인트에 대한 액세스를 제한한다. 따라서 ADMIN 역할을 가진 사용자만 이 엔드포인트에 액세스하고 기존 직원을 업데이트할 수 있다.

마지막으로 `@EnableWebFluxSecurity` 주석은 일부 기본 구성과 함께 Spring Security WebFlux 지원을 추가한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-webflux](https://www.baeldung.com/spring-webflux)
