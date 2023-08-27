---
title: Spring MVC 사용자 정의 유효성 검사
author: dejavuhyo
date: 2023-08-28 08:15:00 +0900
categories: [Framework, Spring]
tags: [spring-validator, custom-validator, validator, 스프링-유효성, 사용자-유효성]
---

## 1. 설정
API의 이점을 활용하기 위해 `pom.xml` 파일에 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.0.10.Final</version>
</dependency>
```

Spring Boot를 사용하는 경우 spring-boot-starter-web만 추가할 수 있으며, 이는 hibernate-validator 종속성도 가져온다.

## 2. 맞춤형 검증
사용자 정의 유효성 검사기를 생성하려면 자체 주석을 롤아웃하고 이를 모델에서 사용하여 유효성 검사 규칙을 시행해야 한다.

전화번호를 확인하는 사용자 정의 유효성 검사기를 만든다. 전화번호는 8자리 이상 11자리 이하의 숫자여야 한다.

## 3. 새로운 주석
주석을 정의하기 위해 새로운 `@interface`를 만든다.

```java
@Documented
@Constraint(validatedBy = ContactNumberValidator.class)
@Target( { ElementType.METHOD, ElementType.FIELD })
@Retention(RetentionPolicy.RUNTIME)
public @interface ContactNumberConstraint {
    String message() default "Invalid phone number";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

`@Constraint` 주석을 사용하여 필드의 유효성을 검사할 클래스를 정의했다. `message()`는 사용자 인터페이스에 표시되는 오류 메시지이다. 마지막으로 추가 코드는 대부분 Spring 표준을 준수하는 상용구 코드이다.

## 4. 유효성 검사기 만들기
유효성 검사 규칙을 적용하는 유효성 검사기 클래스를 만든다.

```java
public class ContactNumberValidator implements 
  ConstraintValidator<ContactNumberConstraint, String> {

    @Override
    public void initialize(ContactNumberConstraint contactNumber) {
    }

    @Override
    public boolean isValid(String contactField,
      ConstraintValidatorContext cxt) {
        return contactField != null && contactField.matches("[0-9]+")
          && (contactField.length() > 8) && (contactField.length() < 14);
    }

}
```

유효성 검사 클래스는 ConstraintValidator 인터페이스를 구현하고 isValid 메서드도 구현해야 한다. 이 방법으로 유효성 검사 규칙을 정의했다.

유효성 검사기가 어떻게 작동하는지 보여주기 위해 간단한 유효성 검사 규칙을 사용한다.

ConstraintValidator는 주어진 객체에 대해 주어진 제약 조건을 검증하는 논리를 정의한다. 구현은 다음 제한사항을 준수해야 한다.

* 객체는 매개변수화되지 않은 유형으로 해석되어야 한.

* 객체의 일반 매개변수는 제한되지 않은 와일드카드 유형이어야 한다.

## 5. 검증 주석 적용
유효성 검사 규칙을 적용하기 위해 하나의 필드가 있는 간단한 클래스를 만들었다. 유효성을 검사할 주석이 달린 필드를 설정한다.

```java
@ContactNumberConstraint
private String phone;
```

문자열 필드를 정의하고 사용자 정의 주석 `@ContactNumberConstraint`로 주석을 달았다. 컨트롤러에서 매핑을 생성하고 오류를 처리했다.

```java
@Controller
public class ValidatedPhoneController {
 
    @GetMapping("/validatePhone")
    public String loadFormPage(Model m) {
        m.addAttribute("validatedPhone", new ValidatedPhone());
        return "phoneHome";
    }
    
    @PostMapping("/addValidatePhone")
    public String submitForm(@Valid ValidatedPhone validatedPhone,
      BindingResult result, Model m) {
        if(result.hasErrors()) {
            return "phoneHome";
        }
        m.addAttribute("message", "Successfully saved phone: "
          + validatedPhone.toString());
        return "phoneHome";
    }   
}
```

단일 JSP 페이지가 있는 간단한 컨트롤러를 정의하고 submitForm 메소드를 사용하여 전화번호 유효성 검사를 시행했다.

## 6. 뷰
뷰는 단일 필드가 있는 양식이 있는 기본 JSP 페이지이다. 사용자가 양식을 제출하면 사용자 정의 유효성 검사기에 의해 필드의 유효성이 검사되고 유효성 검사 성공 또는 실패 메시지와 함께 동일한 페이지로 리디렉션된다.

```jsp
<form:form 
  action="/${pageContext.request.contextPath}/addValidatePhone"
  modelAttribute="validatedPhone">
    <label for="phoneInput">Phone: </label>
    <form:input path="phone" id="phoneInput" />
    <form:errors path="phone" cssClass="error" />
    <input type="submit" value="Submit" />
</form:form>
```

## 7. 테스트
컨트롤러를 테스트하여 적절한 응답과 보기를 제공하는지 확인한다.

```java
@Test
public void givenPhonePageUri_whenMockMvc_thenReturnsPhonePage(){
    this.mockMvc.
      perform(get("/validatePhone")).andExpect(view().name("phoneHome"));
}
```

또한 사용자 입력을 기반으로 필드가 검증되는지 테스트한다.

```java
@Test
public void 
  givenPhoneURIWithPostAndFormData_whenMockMVC_thenVerifyErrorResponse() {
 
    this.mockMvc.perform(MockMvcRequestBuilders.post("/addValidatePhone").
      accept(MediaType.TEXT_HTML).
      param("phoneInput", "123")).
      andExpect(model().attributeHasFieldErrorCode(
          "validatedPhone","phone","ContactNumberConstraint")).
      andExpect(view().name("phoneHome")).
      andExpect(status().isOk()).
      andDo(print());
}
```

테스트에서는 사용자에게 "123"을 입력했는데 예상한 대로 모든 것이 작동하고 클라이언트 측에 오류가 표시된다.

## 8. 사용자 정의 클래스 수준 검증
클래스의 두 개 이상의 속성을 검증하기 위해 클래스 수준에서 사용자 정의 검증 주석을 정의할 수도 있다.

일반적인 사용 사례는 클래스의 두 필드에 일치하는 값이 있는지 확인하는 것이다.

### 1) 주석 생성
나중에 클래스에 적용할 수 있는 FieldsValueMatch라는 새 주석을 추가한다. 주석에는 비교할 필드의 이름을 나타내는 두 개의 매개변수 field 및 fieldMatch가 있다.

```java
@Constraint(validatedBy = FieldsValueMatchValidator.class)
@Target({ ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
public @interface FieldsValueMatch {

    String message() default "Fields values don't match!";

    String field();

    String fieldMatch();

    @Target({ ElementType.TYPE })
    @Retention(RetentionPolicy.RUNTIME)
    @interface List {
        FieldsValueMatch[] value();
    }
}
```

사용자 정의 주석에는 클래스에 여러 FieldsValueMatch 주석을 정의하기 위한 List 하위 인터페이스도 포함되어 있다.

### 2) 유효성 검사기 만들기
유효성 검사 논리를 포함할 FieldsValueMatchValidator 클래스를 추가한다.

```java
public class FieldsValueMatchValidator 
  implements ConstraintValidator<FieldsValueMatch, Object> {

    private String field;
    private String fieldMatch;

    public void initialize(FieldsValueMatch constraintAnnotation) {
        this.field = constraintAnnotation.field();
        this.fieldMatch = constraintAnnotation.fieldMatch();
    }

    public boolean isValid(Object value, 
      ConstraintValidatorContext context) {

        Object fieldValue = new BeanWrapperImpl(value)
          .getPropertyValue(field);
        Object fieldMatchValue = new BeanWrapperImpl(value)
          .getPropertyValue(fieldMatch);
        
        if (fieldValue != null) {
            return fieldValue.equals(fieldMatchValue);
        } else {
            return fieldMatchValue == null;
        }
    }
}
```

`isValid()` 메소드는 두 필드의 값을 검색하고 동일한지 확인한다.

### 3) 주석 적용
사용자 등록에 필요한 데이터를 위한 NewUserForm 모델 클래스를 만든다. 두 개의 값을 다시 입력하기 위한 두 개의 verifyEmail 및 verifyPassword 속성과 함께 두 개의 이메일 및 비밀번호 속성이 있다.

해당하는 일치 필드에 대해 확인할 두 개의 필드가 있으므로 NewUserForm 클래스에 두 개의 `@FieldsValueMatch` 주석을 추가한다. 하나는 이메일 값용이고 다른 하나는 비밀번호 값용이다.

```java
@FieldsValueMatch.List({ 
    @FieldsValueMatch(
      field = "password", 
      fieldMatch = "verifyPassword", 
      message = "Passwords do not match!"
    ), 
    @FieldsValueMatch(
      field = "email", 
      fieldMatch = "verifyEmail", 
      message = "Email addresses do not match!"
    )
})
public class NewUserForm {
    private String email;
    private String verifyEmail;
    private String password;
    private String verifyPassword;

    // standard constructor, getters, setters
}
```

Spring MVC에서 모델을 검증하기 위해 `@Valid` 주석이 달린 NewUserForm 객체를 수신하고 검증 오류가 있는지 확인하는 `/user` POST 매핑을 사용하여 컨트롤러를 생성한다.

```java
@Controller
public class NewUserController {

    @GetMapping("/user")
    public String loadFormPage(Model model) {
        model.addAttribute("newUserForm", new NewUserForm());
        return "userHome";
    }

    @PostMapping("/user")
    public String submitForm(@Valid NewUserForm newUserForm, 
      BindingResult result, Model model) {
        if (result.hasErrors()) {
            return "userHome";
        }
        model.addAttribute("message", "Valid form");
        return "userHome";
    }
}
```

### 4) 주석 테스트
사용자 정의 클래스 수준 주석을 확인하기 위해 일치하는 정보를 `/user` 엔드포인트로 보낸 다음 응답에 오류가 없는지 확인하는 JUnit 테스트를 작성한다.

```java
public class ClassValidationMvcTest {
  private MockMvc mockMvc;
    
    @Before
    public void setup(){
        this.mockMvc = MockMvcBuilders
          .standaloneSetup(new NewUserController()).build();
    }
    
    @Test
    public void givenMatchingEmailPassword_whenPostNewUserForm_thenOk() 
      throws Exception {
        this.mockMvc.perform(MockMvcRequestBuilders
          .post("/user")
          .accept(MediaType.TEXT_HTML).
          .param("email", "john@yahoo.com")
          .param("verifyEmail", "john@yahoo.com")
          .param("password", "pass")
          .param("verifyPassword", "pass"))
          .andExpect(model().errorCount(0))
          .andExpect(status().isOk());
    }
}
```

그런 다음 일치하지 않는 정보를 `/user` 엔드포인트로 보내고 결과에 두 가지 오류가 포함된다고 확인하는 JUnit 테스트도 추가한다.

```java
@Test
public void givenNotMatchingEmailPassword_whenPostNewUserForm_thenOk() 
  throws Exception {
    this.mockMvc.perform(MockMvcRequestBuilders
      .post("/user")
      .accept(MediaType.TEXT_HTML)
      .param("email", "john@yahoo.com")
      .param("verifyEmail", "john@yahoo.commmm")
      .param("password", "pass")
      .param("verifyPassword", "passsss"))
      .andExpect(model().errorCount(2))
      .andExpect(status().isOk());
    }
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-custom-validator](https://www.baeldung.com/spring-mvc-custom-validator)
