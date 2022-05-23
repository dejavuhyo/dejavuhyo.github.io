---
title: Spring Controller 파라미터 값 받는 방법
author: dejavuhyo
date: 2022-05-23 22:15:00 +0900
categories: [Application, Framework]
tags: [spring-parameter, controller-parameter, httpservletrequest, requestparam, requestbody, modelattribute, 컨트롤러-파라미터, 파라미터-받기]
---

## 1. HttpServletRequest
HttpServletRequest의 getParameter() 메서드를 이용하여 파라미터값을 받을 수 있다. 이때 파라미터로 보낸 변수명과 `getParameter("변수명")`에 들어갈 변수명이 일치해야 한다.

```java
@RequestMapping("/main.do")
public String main(HttpServletRequest request) {
    String id = request.getParameter("id");
    System.out.prinln(id);
    return "main";
}
```

## 2. @RequestParam
`@RequestParam` 어노테이션을 이용하여 파라미터값을 받는 방법이다.

| 이름 | 타입 | 설명 |
|:---:|:---:|:---:|
| name, value (Alias for name) | String | 파라미터 이름 |
| required | bollean | 해당 파라미터의 필수 여부, 기본값 true |
| defaultValue | String | 해당 파라미터 기본값 |

> requried="true"로 설정하고 파라미터가 넘어오지 않으면 HTTP 400 에러가 발생한다.

```java
@RequestMapping("/main.do")
public String main(@RequestParam(value="id", required="false", defaultValue="data") String id) {
    System.out.prinln(id);
    return "main";
}
```

`@RequestParam` 어노테이션을 사용하지 않고 파라미터값을 바로 받을 수 있다. 이 경우는 필수 파라미터값이 false로 설정이 되고, 변수명과 동일한 파라미터값만 받을 수 있게 되며 기본값 설정을 할 수 없다.

```java
@RequestMapping("/main.do")
public String main(String id) {
    System.out.prinln(id);
    return "main";
}
```

## 3. @RequestBody
`@RequestBody` 어노테이션을 사용할 경우 반드시 POST 형식으로 응답받는 구조여야 한다. 예를들어 `JSON`이나 `XML` 데이터를 받을 때 사용한다.

```java
@PostMapping("/main.do")
public String main(@RequestBody UserVO userVO) {
    System.out.prinln(userVO.getId());
    return "main";
}
```

* UserVO.java

```java
@Data
public Class UserVO {
    private String id;
    private String pw;
    private String name;
}
```

> Lombok이 설치된 경우 `@Data`를 사용한다. Lombok이 설치되지 않은 경우 getter, setter 메서드를 만들어준다.

이 어노테이션을 생략하고 간단하게 받을 수도 있다. 이 경우는 변수명과 동일한 파라미터값만 받을 수 있다.

String, Long 타입은 `@RequestParam`으로 취급하지만 이외에는 `@ModelAttribute`로 취급한다.

```java
@PostMapping("/main.do")
public String main(String id, UserVO userVO) {
    System.out.prinln(id);
    System.out.prinln(userVO.getId());
    return "main";
}
```

## 4. @ModelAttribute
`@RequestParam`과 비슷하며 1:1로 파라미터를 받을 경우에는 `@RequestParam`, 도메인이나 오브젝트로 파라미터를 받을 경우는 `@ModelAttribute`으로 받을 수 있다.

또한 이 어노테이션을 사용하면 NULL 체크 등의 검증(Validation)작업을 추가로 할 수 있다. 각 멤버 변수마다 valid 옵션을 줄 수 있고, 에러가 날 경우 `BindExeption`이 발생한다.

```java
@PostMapping("/main.do")
public String main(@ModelAttribute("user") UserVO userVO) {
    System.out.prinln(userVO.getId());
    return "main";
}
```

## [출처 및 참고]
* <https://haenny.tistory.com/107>
* <https://doing7.tistory.com/10>
