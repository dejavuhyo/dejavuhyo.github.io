---
title: Spring @PathVariable Annotation
author: dejavuhyo
date: 2023-08-09 08:00:00 +0900
categories: [Framework, Spring]
tags: [spring-pathvariable, pathvariable-annotation, pathvariable]
---

## 1. 간단한 매핑
`@PathVariable` 주석은 기본 키로 엔터티를 식별하는 엔드포인트이다.

```java
@GetMapping("/api/employees/{id}")
@ResponseBody
public String getEmployeesById(@PathVariable String id) {
    return "ID: " + id;
}
```

이 예에서는 `@PathVariable` 주석을 사용하여 `{id}` 변수로 표시되는 URI의 템플릿 부분을 추출한다.

`/api/employees/{id}`에 대한 간단한 GET 요청은 추출된 id 값으로 getEmployeesById를 호출한다.

```text
http://localhost:8080/api/employees/111
---- 
ID: 111
```

## 2. 경로 변수 이름 지정
이전 예제에서는 메서드 매개 변수와 경로 변수의 이름이 동일하므로 템플릿 경로 변수의 이름 정의를 건너뛰었다.

그러나 경로 변수 이름이 다른 경우 `@PathVariable` 주석의 인수에 지정할 수 있다.

```java
@GetMapping("/api/employeeswithvariable/{id}")
@ResponseBody
public String getEmployeesByIdWithVariableName(@PathVariable("id") String employeeId) {
    return "ID: " + employeeId;
}
```

```text
http://localhost:8080/api/employeeswithvariable/1 
---- 
ID: 1
```

명확성을 위해 경로 변수 이름을 `PathVariable("id")` 대신 `@PathVariable(value="id")`로 정의할 수도 있다.

## 3. 단일 요청의 다중 경로 변수
사용 사례에 따라 컨트롤러 메서드에 대한 요청 URI에 여러 메서드 매개 변수가 있는 경로 변수가 두 개 이상 있을 수 있다.

```java
@GetMapping("/api/employees/{id}/{name}")
@ResponseBody
public String getEmployeesByIdAndName(@PathVariable String id, @PathVariable String name) {
    return "ID: " + id + ", name: " + name;
}
```

```text
http://localhost:8080/api/employees/1/bar 
---- 
ID: 1, name: bar
```

`java.util.Map<String, String>` 유형의 메소드 매개변수를 사용하여 둘 이상의 `@PathVariable` 매개변수를 처리할 수도 있다.

```java
@GetMapping("/api/employeeswithmapvariable/{id}/{name}")
@ResponseBody
public String getEmployeesByIdAndNameWithMapVariable(@PathVariable Map<String, String> pathVarsMap) {
    String id = pathVarsMap.get("id");
    String name = pathVarsMap.get("name");
    if (id != null && name != null) {
        return "ID: " + id + ", name: " + name;
    } else {
        return "Missing Parameters";
    }
}
```

```text
http://localhost:8080/api/employees/1/bar 
---- 
ID: 1, name: bar
```

그러나 경로 변수 문자열에 점(.) 문자가 포함된 경우 여러 `@PathVariable` 매개 변수를 처리하는 동안 [문제](https://www.baeldung.com/spring-mvc-pathvariable-dot)가 있다.

## 4. 선택적 경로 변수
Spring에서는 `@PathVariable` 주석이 달린 메서드 매개변수가 기본적으로 필요하다.

```java
@GetMapping(value = { "/api/employeeswithrequired", "/api/employeeswithrequired/{id}" })
@ResponseBody
public String getEmployeesByIdWithRequired(@PathVariable String id) {
    return "ID: " + id;
}
```

주어진 모양에 따라 위의 컨트롤러는 `/api/employeeswithrequired` 및 `/api/employeeswithrequired/1` 요청 경로를 모두 처리해야 한다. 그러나 `@PathVariables`로 주석이 달린 메서드 매개변수는 기본적으로 필수이므로 `/api/employeeswithrequired` 경로로 전송된 요청을 처리하지 않는다.

```text
http://localhost:8080/api/employeeswithrequired 
---- 
{"timestamp":"2020-07-08T02:20:07.349+00:00","status":404,"error":"Not Found","message":"","path":"/api/employeeswithrequired"} 

http://localhost:8080/api/employeeswithrequired/1 
---- 
ID: 111
```

두 가지 다른 방법으로 이를 처리할 수 있다.

### 1) @PathVariable을 필요하지 않음으로 설정
`@PathVariable`의 필수 속성을 false로 설정하여 선택 사항으로 만들 수 있다. 따라서 이전 예제를 수정하여 경로 변수를 사용하거나 사용하지 않고 URI 버전을 처리할 수 있다.

```java
@GetMapping(value = { "/api/employeeswithrequiredfalse", "/api/employeeswithrequiredfalse/{id}" })
@ResponseBody
public String getEmployeesByIdWithRequiredFalse(@PathVariable(required = false) String id) {
    if (id != null) {
        return "ID: " + id;
    } else {
        return "ID missing";
    }
}
```

```text
http://localhost:8080/api/employeeswithrequiredfalse 
---- 
ID missing
```

### 2) java.util.Optional 사용
Spring 4.1이 도입된 이후로 `java.util.Optional<T>` (Java 8+에서 사용 가능)를 사용하여 필수가 아닌 경로 변수를 처리할 수도 있다.

```java
@GetMapping(value = { "/api/employeeswithoptional", "/api/employeeswithoptional/{id}" })
@ResponseBody
public String getEmployeesByIdWithOptional(@PathVariable Optional<String> id) {
    if (id.isPresent()) {
        return "ID: " + id.get();
    } else {
        return "ID missing";
    }
}
```

이제 요청에 경로 변수 ID를 지정하지 않으면 기본 응답을 얻는다.

```text
http://localhost:8080/api/employeeswithoptional 
----
ID missing 
```

### 3) Map<String, String> 유형의 메소드 매개변수 사용
앞에서 본 것처럼 `java.util.Map` 유형의 단일 메서드 매개변수를 사용하여 요청 URI의 모든 경로 변수를 처리할 수 있다. 이 전략을 사용하여 선택적 경로 변수를 처리할 수도 있다.

```java
@GetMapping(value = { "/api/employeeswithmap/{id}", "/api/employeeswithmap" })
@ResponseBody
public String getEmployeesByIdWithMap(@PathVariable Map<String, String> pathVarsMap) {
    String id = pathVarsMap.get("id");
    if (id != null) {
        return "ID: " + id;
    } else {
        return "ID missing";
    }
}
```

## 5. @PathVariable 의 기본값
기본적으로 `@PathVariable` 주석이 달린 메서드 매개 변수의 기본값을 정의하는 조항은 없다. 그러나 위에서 설명한 것과 동일한 전략을 사용하여 `@PathVariable`의 기본값 사례를 충족할 수 있다. 경로 변수에서 null을 확인하기만 하면 된다.

예를 들어 `java.util.Optional<String, String>`을 사용하여 경로 변수가 null 인지 여부를 식별할 수 있다. null 이면 기본값으로 요청에 응답할 수 있다.

```java
@GetMapping(value = { "/api/defaultemployeeswithoptional", "/api/defaultemployeeswithoptional/{id}" })
@ResponseBody
public String getDefaultEmployeesByIdWithOptional(@PathVariable Optional<String> id) {
    if (id.isPresent()) {
        return "ID: " + id.get();
    } else {
        return "ID: Default Employee";
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-pathvariable](https://www.baeldung.com/spring-pathvariable)
