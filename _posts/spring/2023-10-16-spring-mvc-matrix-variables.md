---
title: Spring MVC 매트릭스 변수
author: dejavuhyo
date: 2023-10-16 22:10:00 +0900
categories: [Framework, Spring]
tags: [spring-matrix, matrix-variables, matrix]
---

## 1. 구성
Spring MVC 매트릭스 변수를 활성화하려면 구성부터 시작한다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        UrlPathHelper urlPathHelper = new UrlPathHelper();
        urlPathHelper.setRemoveSemicolonContent(false);
        configurer.setUrlPathHelper(urlPathHelper);
    }
}
```

그렇지 않으면 기본적으로 비활성화된다.

## 2. 행렬변수 활용방법
이러한 변수는 경로의 어느 부분에나 나타날 수 있으며 문자 같음(`=`)은 값을 제공하는데 사용되며 세미콜론(`;`)은 각 행렬 변수를 구분하는데 사용된다. 동일한 경로에서 동일한 변수 이름을 반복하거나 쉼표(`,`) 문자를 사용하여 다른 값을 구분할 수도 있다.

예에는 직원에 대한 정보를 제공하는 컨트롤러가 있다. 각 직원에게는 작업 영역이 있으며 해당 속성으로 검색할 수 있다. 검색에 다음 요청을 사용할 수 있다.

```text
http://localhost:8080/spring-mvc-java-2/employeeArea/workingArea=rh,informatics,admin
```

또는 다음과 같다.

```text
http://localhost:8080/spring-mvc-java-2/employeeArea/workingArea=rh;workingArea=informatics;workingArea=admin
```

Spring MVC에서 이러한 변수를 참조하려면 `@MatrixVariable` 주석을 사용해야 한다.

예제에서는 Employee 클래스를 사용한다.

```java
public class Employee {

    private long id;
    private String name;
    private String contactNumber;

    // standard setters and getters
}
```

또한 Company 클래스도 있다.

```java
public class Company {

    private long id;
    private String name;

    // standard setters and getters
}
```

이 두 클래스는 요청 매개변수를 바인딩한다.

## 3. 행렬 변수 속성 정의
변수에 대한 필수 속성이나 기본 속성을 지정할 수 있다. 다음 예에서는 contactNumber가 필요 하므로 다음과 같이 경로에 포함되어야 한다.

```text
http://localhost:8080/spring-mvc-java-2/employeesContacts/contactNumber=223334411
```

요청은 다음 방법으로 처리된다.

```java
@RequestMapping(value = "/employeesContacts/{contactNumber}", method = RequestMethod.GET)
@ResponseBody
public ResponseEntity<List<Employee>> getEmployeeByContactNumber(
  @MatrixVariable(required = true) String contactNumber) {
    List<Employee> employeesList = new ArrayList<Employee>();
    ...
    return new ResponseEntity<List<Employee>>(employeesList, HttpStatus.OK);
}
```

결과적으로 연락처 번호가 223334411인 모든 직원을 얻게 된다.

## 4. 보완 매개변수
행렬 변수는 경로 변수를 보완할 수 있다.

예를 들어 직원의 이름을 검색할 때 연락처 번호의 시작 번호를 포함할 수도 있다.

이 검색에 대한 요청은 다음과 같아야 한다.

```text
http://localhost:8080/spring-mvc-java-2/employees/John;beginContactNumber=22001
```

요청은 다음 방법으로 처리된다.

```java
@RequestMapping(value = "/employees/{name}", method = RequestMethod.GET)
@ResponseBody
public ResponseEntity<List<Employee>> getEmployeeByNameAndBeginContactNumber(
  @PathVariable String name, @MatrixVariable String beginContactNumber) {
    List<Employee> employeesList = new ArrayList<Employee>();
    ...
    return new ResponseEntity<>(employeesList, HttpStatus.OK);
}
```

결과적으로 연락처 번호가 22001 이거나 이름이 John인 모든 직원을 얻게 된다.

## 5. 모든 행렬 변수 바인딩
어떤 이유로든 경로에서 사용할 수 있는 모든 변수를 가져오려면 해당 변수를 Map에 바인딩하면 된다.

```text
http://localhost:8080/spring-mvc-java-2/employeeData/id=1;name=John;contactNumber=2200112334
```

이 요청은 다음 방법으로 처리된다.

```java
@GetMapping("employeeData/{employee}")
@ResponseBody
public ResponseEntity<Map<String, String>> getEmployeeData(
  @MatrixVariable Map<String, String> matrixVars) {
    return new ResponseEntity<>(matrixVars, HttpStatus.OK);
}
```

경로의 특정 부분의 행렬 변수에 대한 바인딩을 제한할 수 있다. 예를 들어, 다음과 같은 요청이 있는 경우이다.

```text
http://localhost:8080/spring-mvc-java-2/companyEmployee/id=2;name=Xpto/employeeData/id=1;name=John;contactNumber=2200112334
```

그리고 EmployeeData에 속하는 모든 변수만 가져오길 원한다. 그런 다음 다음을 입력 매개변수로 사용해야 한다.

```java
@RequestMapping(value = "/companyEmployee/{company}/employeeData/{employee}", method = RequestMethod.GET)
@ResponseBody
public ResponseEntity<Map<String, String>> getEmployeeDataFromCompany(
  @MatrixVariable(pathVar = "employee") Map<String, String> matrixVars) {
  ...
}
```

## 6. 부분 바인딩
단순성과는 별개로 유연성은 또 다른 이점이다. 행렬 변수는 다양한 방식으로 사용될 수 있다. 예를 들어, 각 경로 세그먼트에서 각 변수를 가져올 수 있다. 다음 요청을 확인한다.

```text
http://localhost:8080/spring-mvc-java-2/companyData/id=2;name=Xpto/employeeData/id=1;name=John;contactNumber=2200112334
```

companyData 세그먼트의 행렬 변수 이름만 알고 싶다면 다음을 입력 매개변수로 사용해야 한다.

```java
@MatrixVariable(value="name", pathVar="company") String name
```

## 7. 방화벽 설정
애플리케이션이 Spring Security를 ​​사용하는 경우 기본적으로 StrictHttpFirewall이 사용된다. 이는 세미콜론 구분 기호가 있는 매트릭스 변수를 포함하여 악성으로 보이는 요청을 차단한다.

애플리케이션 구성에서 이 구현을 사용자 정의하고 악의적일 수 있는 다른 요청을 거부하면서 이러한 변수를 허용할 수 있다.

그러나 이렇게 하면 애플리케이션이 공격에 노출될 수 있다. 따라서 애플리케이션 및 보안 요구 사항을 주의 깊게 분석한 후에만 이를 구현해야 한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-matrix-variables](https://www.baeldung.com/spring-mvc-matrix-variables)
