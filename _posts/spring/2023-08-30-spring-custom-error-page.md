---
title: Spring MVC 사용자 정의 오류 페이지
author: dejavuhyo
date: 2023-08-30 08:30:00 +0900
categories: [Framework, Spring]
tags: [spring-custom-error, spring-error, custom-error, 스프링-오류, 오류-페이지]
---

## 1. 간단한 단계
따라야 할 간단한 단계이다.

* 오류가 생성될 때마다 오류를 처리하는 메서드에 매핑되는 `web.xml`에 단일 URL `/errors`를 지정한다.

* `/errors` 매핑을 사용하여 ErrorController라는 컨트롤러를 만든다.

* 런타임 시 HTTP 오류 코드를 파악하고 HTTP 오류 코드에 따라 메시지를 표시한다. 예를 들어, 404 에러가 생성되면 사용자에게 'Resource not found'와 같은 메시지가 표시되어야 하고, 500 오류의 경우 사용자는 'Sorry! An Internal Server Error was generated at our end'로 표시되어야 한다.

## 2. web.xml
`web.xml`에 다음 줄을 추가한다.

```xml
<error-page>
    <location>/errors</location>
</error-page>
```

이 기능은 Servlet 버전 3.0 이상에서만 사용할 수 있다.

앱 내에서 생성된 모든 오류는 HTTP 오류 코드와 연결된다. 예를 들어, 사용자가 URL `/invalidUrl`을 브라우저에 입력했지만 Spring 내부에 그러한 RequestMapping이 정의되어 있지 않다고 가정한다. 그런 다음 기본 웹 서버에서 생성된 HTTP 코드 404이다. 방금 `web.xml`에 추가한 줄은 Spring에게 URL `/errors`에 매핑된 메서드에 작성된 논리를 실행하도록 지시한다.

참고할 점은 해당 Java Servlet 구성에는 불행히도 오류 페이지 설정을 위한 API가 없기 때문이다. 따라서 이 경우에는 실제로 `web.xml`이 필요하다.

## 3. Controller
ErrorController를 생성한다. 오류를 가로채고 오류 페이지를 표시하는 단일 통합 메서드를 만든다.

```java
@Controller
public class ErrorController {

    @RequestMapping(value = "errors", method = RequestMethod.GET)
    public ModelAndView renderErrorPage(HttpServletRequest httpRequest) {
        
        ModelAndView errorPage = new ModelAndView("errorPage");
        String errorMsg = "";
        int httpErrorCode = getErrorCode(httpRequest);

        switch (httpErrorCode) {
            case 400: {
                errorMsg = "Http Error Code: 400. Bad Request";
                break;
            }
            case 401: {
                errorMsg = "Http Error Code: 401. Unauthorized";
                break;
            }
            case 404: {
                errorMsg = "Http Error Code: 404. Resource not found";
                break;
            }
            case 500: {
                errorMsg = "Http Error Code: 500. Internal Server Error";
                break;
            }
        }
        errorPage.addObject("errorMsg", errorMsg);
        return errorPage;
    }
    
    private int getErrorCode(HttpServletRequest httpRequest) {
        return (Integer) httpRequest
          .getAttribute("javax.servlet.error.status_code");
    }
}
```

## 4. Front-End
데모 목적으로 오류 페이지를 매우 간단하고 간결하게 유지한다. 이 페이지에는 흰색 화면에 표시되는 메시지만 포함된다. `errorPage.jsp`라는 jsp 파일을 만든다.

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ page session="false"%>
<html>
<head>
    <title>Home</title>
</head>
<body>
    <h1>${errorMsg}</h1>
</body>
</html>
```

## 5. 테스트
모든 애플리케이션에서 발생하는 가장 일반적인 두 가지 오류인 HTTP 404 오류와 HTTP 500 오류를 시뮬레이션한다.

서버를 실행하고 `localhost:8080/spring-mvc-xml/invalidUrl`로 이동한다. 이 URL이 존재하지 않기 때문에 `Http Error Code : 404. Resource not found`라는 메시지와 함께 오류 페이지가 표시된다.

핸들러 메서드 중 하나가 NullPointerException을 던지면 어떤 일이 발생하는지 확인한다. ErrorController에 다음 메서드를 추가한다.

```java
@RequestMapping(value = "500Error", method = RequestMethod.GET)
public void throwRuntimeException() {
    throw new NullPointerException("Throwing a null pointer exception");
}
```

`localhost:8080/spring-mvc-xml/500Error`로 이동한다. `Http Error Code : 500. Internal Server Error` 메시지와 함께 흰색 화면이 나타난다.

## [출처 및 참고]
* [https://www.baeldung.com/custom-error-page-spring-mvc](https://www.baeldung.com/custom-error-page-spring-mvc)
