---
title: Controller와 RestController의 차이점
author: dejavuhyo
date: 2022-11-14 19:40:00 +0900
categories: [Application, Framework]
tags: [controller-restcontroller, controller, restcontroller, controller-view, controller-data, restcontroller-data, controller-return, restcontroller-return, controller-restcontroller-차이점, 컨트롤러-차이점]
---

## 1. @Controller

### 1) View 반환
전통적인 Spring MVC의 컨트롤러인 `@Controller`는 주로 View를 반환하기 위해 사용한다. 아래와 같은 과정을 통해 Spring MVC Container는 Client의 요청으로부터 View를 반환한다.

![controller-view](/assets/img/2022-11-14-controller-vs-restcontroller/controller-view.png)

① Client는 URI 형식으로 웹 서비스에 요청을 보낸다.

② DispatcherServlet이 요청을 위임할 HandlerMapping을 찾는다.

③ HandlerMapping을 통해 요청을 Controller로 위임한다.

④ Controller는 요청을 처리한 후에 ViewName을 반환한다.

⑤ DispatcherServlet은 ViewResolver를 통해 ViewName에 해당하는 View를 찾아 사용자에게 반환한다.

Controller가 반환한 뷰의 이름으로부터 View를 렌더링하기 위해서는 ViewResolver가 사용되며, ViewResolver 설정에 맞게 View를 찾아 렌더링한다.

### 2) Data 반환
컨트롤러에서 데이터를 반환하기 위해 `@ResponseBody` 어노테이션을 사용한다. 이를 통해 Controller도 Json 형태로 데이터를 반환할 수 있다.

![controller-data](/assets/img/2022-11-14-controller-vs-restcontroller/controller-data.png)

① Client는 URI 형식으로 웹 서비스에 요청을 보낸다.

② DispatcherServlet이 요청을 위임할 HandlerMapping을 찾는다.

③ HandlerMapping을 통해 요청을 Controller로 위임한다.

④ Controller는 요청을 처리한 후에 객체를 반환한다.

⑤ 반환되는 객체는 Json으로 Serialize 되어 사용자에게 반환된다.

컨트롤러를 통해 객체를 반환할 때는 일반적으로 ResponseEntity로 감싸서 반환한다. 그리고 객체를 반환하기 위해서는 ViewResolver 대신에 HttpMessageConverter가 동작한다. HttpMessageConverter에는 여러 Converter가 등록되어 있고, 반환해야 하는 데이터에 따라 사용되는 Converter가 달라진다.

단순 문자열인 경우에는 StringHttpMessageConverter가 사용되고, 객체인 경우에는 MappingJackson2HttpMessageConverter가 사용되며, 데이터 종류에 따라 서로 다른 MessageConverter가 작동하게 된다.

Spring은 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해 적합한 HttpMessageConverter를 선택하여 이를 처리한다. MessageConverter가 동작하는 시점은 HandlerAdapter와 Controller가 요청을 주고받는 시점이다.

④에서는 메시지를 객체로, ⑥에서는 객체를 메시지로 변환하는 데 메시지 컨버터가 사용된다.

* @Controller 예제

```java
@Controller
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping(value = "/users")
    public @ResponseBody ResponseEntity<User> findUser(@RequestParam("userName") String userName){
        return ResponseEntity.ok(userService.findUser(user));
    }
    
    @GetMapping(value = "/users/detailView")
    public String detailView(Model model, @RequestParam("userName") String userName){
        User user = userService.findUser(userName);
        model.addAttribute("user", user);
        return "/users/detailView";
    }
}
```

findUser는 User 객체를 ResponseEntity로 감싸서 반환하고 있고, User를 json으로 반환하기 위해 `@ResponseBody` 어노테이션을 사용하고 있다. detailView 함수에서는 View를 전달해주고 있기 때문에 String을 반환 값으로 사용한다.

## 2. @RestController
`@RestController`는 `@Controller`에 `@ResponseBody`가 추가되었다. RestController의 주 용도는 Json 형태로 객체 데이터를 반환하는 것이다.

데이터를 응답으로 제공하는 REST API를 개발할 때 주로 사용하며, 객체를 ResponseEntity로 감싸서 반환한다. 동작 과정도 `@Controller`에 `@ReponseBody`를 붙인 것과 동일하다.

![rest-controller](/assets/img/2022-11-14-controller-vs-restcontroller/rest-controller.png)

① Client는 URI 형식으로 웹 서비스에 요청을 보낸다.

② DispatcherServlet이 요청을 위임할 HandlerMapping을 찾는다.

③ HandlerMapping을 통해 요청을 Controller로 위임한다.

④ Controller는 요청을 처리한 후에 객체를 반환한다.

⑤ 반환되는 객체는 Json으로 Serialize 되어 사용자에게 반환된다.

* @RestController 예제

```java
@RestController
@RequestMapping("/user")
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping(value = "/users")
    public User findUser(@RequestParam("userName") String userName){
        return userService.findUser(user);
    }

    @GetMapping(value = "/users")
    public ResponseEntity<User> findUserWithResponseEntity(@RequestParam("userName") String userName){
        return ResponseEntity.ok(userService.findUser(user));
    }
}
```

findUser는 User 객체를 그대로 반환하고 있다. 이러한 경우의 문제는 클라이언트가 예상하는 HttpStatus를 설정해줄 수 없다.

예를 들어 어떤 객체의 생성 요청이라면 201 CREATED를 기대하지만, 객체를 그대로 반환하면 HttpStatus를 설정해줄 수 없다. 그래서 객체를 상황에 맞는 ResponseEntity로 감싸서 반환해주어야 한다.

## [출처 및 참고]
* <https://mangkyu.tistory.com/49>
* <https://www.genuitec.com/spring-frameworkrestcontroller-vs-controller/>
