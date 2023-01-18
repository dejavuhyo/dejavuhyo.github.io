---
title: Spring Controller에서 데이터 받는 방법
author: dejavuhyo
date: 2023-01-18 19:55:00 +0900
categories: [Application, Framework]
tags: [receive-data, controller-receive-data, controller-data, httpservletrequest, getparameter, parameter, requestparam, modelattribute, requestbody, pathvariable, controller-데이터-받기, 데이터-받기, controller-파라미터]
---

## 1. 데이터 받는 방법
Controller에서 데이터를 받는 방법에는 `HttpServletRequest`, `@RequestParam`, `@RequestBody`, `@ModelAtrribute`, `@PathVariable`이 있다.

## 2. HttpServletRequest.getParameter()
`getParameter()`는 String 값을 반환한다.

```java
@GetMapping("/getInfo");
public void getInfo(HttpServletRequest request){
    log.info("get parameter" + request.getParameter("id"));
    log.info("referer" + request.getHeader("referer")); // 이전 URL
}
```

## 3. @RequestParam
Query String 방식으로 URL을 통해 parameter로 값을 받는다. 속성으로는 value, required, defaultValue가 있다.

required는 파라미터 필수 여부이며, 디폴트값은 true이다.

required="false"이고, 파라미터를 받지 않았다면 defaultVaule로 파라미터의 기본값을 설정해줄 수 있다.

```java
@GetMapping("/getInfo");
public void getInfo(@RequestParam(value="id", required="false", defaultValue="0")String id){
    log.info("get parameter" + id);
}
```

## 4. @ModelAttribute
파라미터를 객체로 받는다. (JavaObject로 매핑)

`@RequestParam`과 비슷한 방법이지만, 파라미터를 1:1로 하나씩 받지 않고, 여러 파라미터를 받아 자바의 객체로 매핑한다. (객체로 한 번에 바인딩해서 받음)

변수의 Setter 함수가 없으면 값을 받지 못한다.

```java
@GetMapping("/getInfo");
public void getInfo(@ModelAttribute("user") UserVO user){
    log.info("get parameter" + user.getId());
}
```

## 5. @RequestBody
JSON이나 XML과 같은 데이터를 받거나 DTO/VO 객체 전체를 받을 경우 사용한다.

`@ModelAttribute`와 같이 파라미터를 객체로 받는다. 그러나 `@ModelAttribute`와 다른 점은 MessageConverter를 이용하여 자바의 객체로 변환한다.

* POST 요청만 가능하다.
  - MessageConverter는 HTTP 요청의 Body 내용을 자바의 객체로 변환시킨다. GET 방식의 메소드는 Body가 존재하지 않기 때문에 에러를 발생시킨다.

* JSON 데이터 받을 때 주로 사용한다.
  - JSON이나 XML과 같은 데이터를 MessageConverter를 이용해서 자바의 객체로 변환한다.

* Setter가 없어도 된다.
  - `@ModelAttribute`는 자바의 객체로 1:1 매핑이기에 Setter가 필수지만, `@RequestBody`는 MessageConverter를 통한 자바의 객체로 변환이기 때문에 Setter가 없어도 된다.

```java
@PosMapping("/getInfo");
public void getInfo(@RequestBody UserVO user){
    log.info("get parameter" + user.getId());
}
```

어노테이션을 생략하고 간단하게 받을 수 있다. 이 경우에는 변수명과 동일한 파라미터 값만 받을 수 있다.

String, Long 타입은 `@RequestParam`으로 취급하지만 이외에는 `@ModelAttribute`로 취급한다.

```java
@PostMapping("/getInfo");
public void getInfo(String id, UserVO user){
    log.info("get parameter" + id);
    log.info("get parameter" + user.getId());
}
```

## 6. @PathVariable
URL 정의 부분과 메소드 내에 파라미터에 정의하여 사용한다. 즉, RequestMapping에 {id} 이라는 부분에 매핑된다.

null이나 공백값이 들어가는 파라미터는 적용할 수 없다. 값에 마침표(.)가 있다면 마침표 뒤가 잘려서 나온다.

```java
@PosMapping("/getInfo/{id}");
public void getInfo(@PathVariable String id){
    log.info("get parameter" + id);
}
```

변수명을 다르게 사용하면 아래와 같다.

```java
@PosMapping("/getInfo/{id}");
public void getInfo(@PathVariable("id") String userId){
    log.info("get parameter" + userId);
}
```

## [출처 및 참고]
* [https://doing7.tistory.com/10](https://doing7.tistory.com/10)
* [https://ooeunz.tistory.com/99](https://ooeunz.tistory.com/99)
