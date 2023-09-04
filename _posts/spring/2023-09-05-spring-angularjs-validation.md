---
title: Spring MVC 및 AngularJS를 사용한 양식 검증
author: dejavuhyo
date: 2023-09-05 06:40:00 +0900
categories: [Framework, Spring]
tags: [spring-validation, validation, angularjs-validation, 스프링-검증, 검증]
---

## 1. 메이븐 의존성
종속성을 추가한다.

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.7.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.4.0.Final</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.13.0</version>
</dependency>
```

## 2. Spring MVC를 사용한 검증
애플리케이션은 쉽게 우회할 수 있으므로 클라이언트 측 유효성 검사에만 의존해서는 안된다. 부정확하거나 악의적인 값이 저장되거나 애플리케이션 로직이 부적절하게 실행되는 것을 방지하려면 서버 측에서도 입력 값의 유효성을 검사하는 것이 중요하다.

Spring MVC는 JSR 349 ​​Bean 유효성 검사 사양 주석을 사용하여 서버측 유효성 검사를 지원한다. 예에서는 사양의 참조 구현인 `hibernate-validator`를 사용한다.

### 1) 데이터 모델
적절한 유효성 검사 주석이 달린 속성이 있는 User 클래스를 만든다.

```java
public class User {

    @NotNull
    @Email
    private String email;

    @NotNull
    @Size(min = 4, max = 15)
    private String password;

    @NotBlank
    private String name;

    @Min(18)
    @Digits(integer = 2, fraction = 0)
    private int age;

    // standard constructor, getters, setters
}
```

위에서 사용된 주석은 hibernate-validator 라이브러리에 특정한 `@Email` 및 `@NotBlank`를 제외하고 JSR 349 ​​사양에 속한다.

### 2) Spring MVC Controller
새 User 개체를 List에 저장하는데 사용되는 `/user` endpoint를 정의하는 컨트롤러 클래스를 만든다.

요청 매개변수를 통해 수신된 User 객체의 유효성 검사를 활성화하려면 선언 앞에 `@Valid` 주석이 있어야 하며 유효성 검사 오류는 BindingResult 인스턴스에 보관된다.

객체에 유효하지 않은 값이 포함되어 있는지 확인하려면 BindingResult의 `hasErrors()` 메서드를 사용할 수 있다.

`hasErrors()`가 true를 반환 하면 통과하지 못한 유효성 검사와 관련된 오류 메시지가 포함된 JSON 배열을 반환할 수 있다. 그렇지 않으면 객체를 목록에 추가한다.

```java
@PostMapping(value = "/user")
@ResponseBody
public ResponseEntity<Object> saveUser(@Valid User user, 
  BindingResult result, Model model) {
    if (result.hasErrors()) {
        List<String> errors = result.getAllErrors().stream()
          .map(DefaultMessageSourceResolvable::getDefaultMessage)
          .collect(Collectors.toList());
        return new ResponseEntity<>(errors, HttpStatus.OK);
    } else {
        if (users.stream().anyMatch(it -> user.getEmail().equals(it.getEmail()))) {
            return new ResponseEntity<>(
              Collections.singletonList("Email already exists!"), 
              HttpStatus.CONFLICT);
        } else {
            users.add(user);
            return new ResponseEntity<>(HttpStatus.CREATED);
        }
    }
}
```

서버측 유효성 검사는 클라이언트측에서 불가능한 추가 검사를 수행할 수 있다는 이점을 추가한다.

동일한 이메일을 가진 사용자가 이미 존재하는지 확인할 수 있으며, 그렇다면 409 CONFLICT 상태를 반환한다.

또한 사용자 목록을 정의하고 몇 가지 값으로 초기화해야 한다.

```java
private List<User> users = Arrays.asList(
  new User("ana@yahoo.com", "pass", "Ana", 20),
  new User("bob@yahoo.com", "pass", "Bob", 30),
  new User("john@yahoo.com", "pass", "John", 40),
  new User("mary@yahoo.com", "pass", "Mary", 30));
```

사용자 목록을 JSON 개체로 검색하기 위한 매핑도 추가한다.

```java
@GetMapping(value = "/users")
@ResponseBody
public List<User> getUsers() {
    return users;
}
```

Spring MVC 컨트롤러에 필요한 마지막 항목은 애플리케이션의 기본 페이지를 반환하는 매핑이다.

```java
@GetMapping("/userPage")
public String getUserProfilePage() {
    return "user";
}
```

### 3) Spring MVC Configuration
애플리케이션에 기본 MVC 구성을 추가한다.

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.baeldung.springmvcforms")
class ApplicationConfiguration implements WebMvcConfigurer {

    @Override
    public void configureDefaultServletHandling(
      DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    @Bean
    public InternalResourceViewResolver htmlViewResolver() {
        InternalResourceViewResolver bean = new InternalResourceViewResolver();
        bean.setPrefix("/WEB-INF/html/");
        bean.setSuffix(".html");
        return bean;
    }
}
```

### 4) 애플리케이션 초기화
애플리케이션을 실행하기 위해 WebApplicationInitializer 인터페이스를 구현하는 클래스를 생성한다.

```java
public class WebInitializer implements WebApplicationInitializer {

    public void onStartup(ServletContext container) throws ServletException {

        AnnotationConfigWebApplicationContext ctx
          = new AnnotationConfigWebApplicationContext();
        ctx.register(ApplicationConfiguration.class);
        ctx.setServletContext(container);
        container.addListener(new ContextLoaderListener(ctx));

        ServletRegistration.Dynamic servlet 
          = container.addServlet("dispatcher", new DispatcherServlet(ctx));
        servlet.setLoadOnStartup(1);
        servlet.addMapping("/");
    }
}
```

### 5) Curl을 사용하여 Spring Mvc 유효성 검사 테스트
다음 명령과 함께 cURL을 사용하여 API를 테스트할 수 있다.

```shell
curl -i -X POST -H "Accept:application/json" 
  "localhost:8080/spring-mvc-forms/user?email=aaa&password=12&age=12"
```

응답은 기본 오류 메시지가 포함된 배열이다.

```text
[
    "not a well-formed email address",
    "size must be between 4 and 15",
    "may not be empty",
    "must be greater than or equal to 18"
]
```

## 3. AngularJS 검증
클라이언트 측 유효성 검사는 사용자에게 유효한 데이터를 성공적으로 제출하는 방법에 대한 정보를 제공하고 애플리케이션과 계속 상호 작용할 수 있도록 지원하므로 더 나은 사용자 경험을 만드는 데 유용하다.

AngularJS 라이브러리는 양식 필드에 유효성 검사 요구 사항을 추가하고, 오류 메시지를 처리하고, 유효하고 유효하지 않은 양식의 스타일을 지정하는데 탁월한 지원을 제공한다.

유효성 검사 메시지에 사용되는 ngMessages 모듈을 삽입하는 AngularJS 모듈을 만든다.

```javascript
var app = angular.module('app', ['ngMessages']);
```

### 1) AngularJS 서비스
MVC 컨트롤러 메서드를 호출하는 두 가지 메서드가 있다. 하나는 사용자를 저장하고 다른 하나는 사용자 목록을 검색하는 것이다.

```javascript
app.service('UserService',['$http', function ($http) {
	
    this.saveUser = function saveUser(user){
        return $http({
          method: 'POST',
          url: 'user',
          params: {email:user.email, password:user.password, 
            name:user.name, age:user.age},
          headers: 'Accept:application/json'
        });
    }
	
    this.getUsers = function getUsers(){
        return $http({
          method: 'GET',
          url: 'users',
          headers:'Accept:application/json'
        }).then( function(response){
        	return response.data;
        } );
    }

}]);
```

### 2) AngularJS 컨트롤러
UserCtrl 컨트롤러는 UserService를 주입하고 서비스 메서드를 호출하며 응답 및 오류 메시지를 처리한다.

```javascript
app.controller('UserCtrl', ['$scope','UserService', function ($scope,UserService) {
	
	$scope.submitted = false;
	
	$scope.getUsers = function() {
		   UserService.getUsers().then(function(data) {
		       $scope.users = data;
	       });
	   }
    
    $scope.saveUser = function() {
    	$scope.submitted = true;
    	  if ($scope.userForm.$valid) {
            UserService.saveUser($scope.user)
              .then (function success(response) {
                  $scope.message = 'User added!';
                  $scope.errorMessage = '';
                  $scope.getUsers();
                  $scope.user = null;
                  $scope.submitted = false;
              },
              function error(response) {
                  if (response.status == 409) {
                    $scope.errorMessage = response.data.message;
            	  }
            	  else {
                    $scope.errorMessage = 'Error adding user!';
            	  }
                  $scope.message = '';
            });
    	  }
    }
   
   $scope.getUsers();
}]);
```

위의 예에서는 userForm의 `$valid` 속성이 true인 경우에만 서비스 메서드가 호출된다. 하지만 이 경우 중복 이메일에 대한 추가 확인이 있는데, 이는 서버에서만 수행할 수 있으며 `error()` 함수에서 별도로 처리된다.

또한 양식이 제출되었는지 여부를 알려주는 제출된 변수가 정의되어 있다.

처음에 이 변수는 false가 되며 `saveUser()` 메서드를 호출하면 true가 된다. 사용자가 양식을 제출하기 전에 확인 메시지를 표시하지 않으려면 제출된 변수를 사용하여 이를 방지할 수 있다.

### 3) AngularJS 유효성 검사를 사용한 양식
AngularJS 라이브러리와 AngularJS 모듈을 사용하려면 `user.html` 페이지에 스크립트를 추가한다.

```javascript
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.6/angular.min.js"></script>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.0/angular-messages.js"></script>
<script src="js/app.js"></script>
```

그런 다음 `ng-app` 및 `ng-controller` 속성을 설정하여 모듈과 컨트롤러를 사용할 수 있다.

```html
<body ng-app="app" ng-controller="UserCtrl">
```

HTML 양식이다.

```html
<form name="userForm" method="POST" novalidate 
  ng-class="{'form-error':submitted}" ng-submit="saveUser()" >
...
</form>
```

기본 HTML5 유효성 검사를 방지하고 이를 자체 유효성 검사로 바꾸려면 양식에 novalidate 속성을 설정해야 한다.

`ng-class` 속성은 제출된 변수의 값이 true인 경우 양식 오류 CSS 클래스를 양식에 동적으로 추가한다.

`ng-submit` 속성은 양식이 제출될 때 호출되는 AngularJS 컨트롤러 함수를 정의한다. `ng-click` 대신 `ng-submit`을 사용하면 ENTER 키를 사용하여 양식을 제출하는 데에도 응답한다는 이점이 있다.

사용자 속성에 대한 4개의 입력 필드를 추가한다.

```html
<label class="form-label">Email:</label>
<input type="email" name="email" required ng-model="user.email" class="form-input"/>

<label class="form-label">Password:</label>
<input type="password" name="password" required ng-model="user.password" ng-minlength="4" ng-maxlength="15" class="form-input"/>

<label class="form-label">Name:</label>
<input type="text" name="name" ng-model="user.name" ng-trim="true" required class="form-input" />

<label class="form-label">Age:</label>
<input type="number" name="age" ng-model="user.age" ng-min="18" class="form-input" required/>
```

각 입력 필드에는 `ng-model` 속성을 통해 사용자 변수의 속성에 대한 바인딩이 있다.

유효성 검사 규칙을 설정하기 위해 HTML5 필수 속성과 여러 AngularJS 관련 속성(`ng-minglength`, `ng-maxlength`, `ng-min` 및 `ng-trim`)을 사용한다.

email 필드의 경우 클라이언트 측 이메일 검증을 위해 email 값과 함께 type 속성도 사용한다.

각 필드에 해당하는 오류 메시지를 추가하기 위해 AngularJS는 입력의 `$errors` 객체를 반복하고 각 유효성 검사 규칙에 따라 메시지를 표시하는 `ng-messages` 지시문을 제공한다.

입력 정의 바로 뒤에 이메일 필드에 대한 지시문을 추가한다.

```html
<div ng-messages="userForm.email.$error" 
  ng-show="submitted && userForm.email.$invalid" class="error-messages">
    <p ng-message="email">Invalid email!</p>
    <p ng-message="required">Email is required!</p>
</div>
```

다른 입력 필드에도 유사한 오류 메시지가 추가될 수 있다.

boolean 표현식과 함께 `ng-show` 속성을 사용하여 이메일 필드에 지시문이 표시되는 시점을 제어할 수 있다. 예에서는 필드에 유효하지 않은 값이 있을 때 지시문을 표시한다. 즉, `$invalid` 속성이 true 이고 제출된 변수도 true 이다.

한 필드에 대해 한 번에 하나의 오류 메시지만 표시된다.

`$valid` 속성에 따라 필드가 유효한 경우 입력 필드 뒤에 확인 표시 기호(16진수 코드 문자 ✓로 표시)를 추가할 수도 있다.

```html
<div class="check" ng-show="userForm.email.$valid">✓</div>
```

AngularJS 유효성 검사는 `ng-valid` 및 `ng-invalid`와 같은 CSS 클래스 또는 `ng-invalid-required` 및 `ng-invalid-minlength`와 같은 보다 구체적인 클래스를 사용하여 스타일 지정을 지원한다.

양식의 양식 오류 클래스 내부에 유효하지 않은 입력에 대해 CSS 속성 `border-color:red`를 추가한다.

```css
.form-error input.ng-invalid {
    border-color:red;
}
```

CSS 클래스를 사용하여 오류 메시지를 빨간색으로 표시할 수도 있다.

```css
.error-messages {
    color:red;
}
```

모든 것을 종합한 후 유효한 값과 유효하지 않은 값을 혼합하여 채웠을 때 클라이언트 측 양식 유효성 검사가 어떻게 보이는지 예이다.

![angularjs-form-validation](/assets/img/2023-09-05-spring-angularjs-validation/angularjs-form-validation.png)

## [출처 및 참고]
* [https://www.baeldung.com/validation-angularjs-spring-mvc](https://www.baeldung.com/validation-angularjs-spring-mvc)
