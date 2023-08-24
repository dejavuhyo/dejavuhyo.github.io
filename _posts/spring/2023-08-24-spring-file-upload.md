---
title: Spring 파일 업로드
author: dejavuhyo
date: 2023-08-24 09:25:00 +0900
categories: [Framework, Spring]
tags: [spring-file-upload, file-upload, 파일-업로드]
---

## 1. 커먼즈 파일 업로드
`CommonsMultipartResolver`를 사용하여 파일 업로드를 처리하려면 다음 종속성을 추가해야 한다.

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
```

이제 Spring 구성에서 CommonsMultipartResolver 빈을 정의할 수 있다.

이 MultipartResolver는 업로드를 위한 최대 크기와 같은 속성을 정의하는 일련의 set 메서드와 함께 제공된다.

```java
@Bean(name = "multipartResolver")
public CommonsMultipartResolver multipartResolver() {
    CommonsMultipartResolver multipartResolver = new CommonsMultipartResolver();
    multipartResolver.setMaxUploadSize(100000);
    return multipartResolver;
}
```

여기서 Bean 정의 자체에서 CommonsMultipartResolver의 다른 속성을 제어해야 한다.

## 2. 서블릿 3.0
Servlet 3.0 멀티파트 구문 분석을 사용하려면 몇 가지 응용 프로그램을 구성해야 한다.

먼저 DispatcherServlet 등록에서 MultipartConfigElement를 설정해야 한다.

```java
public class MainWebAppInitializer implements WebApplicationInitializer {

    private String TMP_FOLDER = "/tmp"; 
    private int MAX_UPLOAD_SIZE = 5 * 1024 * 1024; 
    
    @Override
    public void onStartup(ServletContext sc) throws ServletException {
        
        ServletRegistration.Dynamic appServlet = sc.addServlet("mvc", new DispatcherServlet(new GenericWebApplicationContext()));
        appServlet.setLoadOnStartup(1);
        
        MultipartConfigElement multipartConfigElement = new MultipartConfigElement(TMP_FOLDER, MAX_UPLOAD_SIZE, MAX_UPLOAD_SIZE * 2, MAX_UPLOAD_SIZE / 2);
        appServlet.setMultipartConfig(multipartConfigElement);
    }
}
```

MultipartConfigElement 개체에서 저장 위치, 최대 개별 파일 크기, 최대 요청 크기(단일 요청에 여러 파일이 있는 경우) 및 파일 업로드 진행률이 저장 위치로 플러시되는 크기를 구성했다.

이러한 설정은 CommonsMultipartResolver의 경우와 같이 Servlet 3.0에서 MultipartResolver에 등록되는 것을 허용하지 않기 때문에 서블릿 등록 수준에서 적용되어야 한다.

이 작업이 완료되면 Spring 구성에 StandardServletMultipartResolver를 추가할 수 있다.

```java
@Bean
public StandardServletMultipartResolver multipartResolver() {
    return new StandardServletMultipartResolver();
}
```

## 3. 파일 업로드
파일을 업로드하기 위해 `type='file'` 과 함께 HTML 입력 태그를 사용하는 간단한 양식을 작성할 수 있다.

선택한 업로드 처리 구성에 관계없이 양식의 인코딩 속성을 `multipart/form-data`로 설정해야 한다.

이렇게 하면 브라우저가 양식을 인코딩하는 방법을 알 수 있다.

```html
<form:form method="POST" action="/spring-mvc-xml/uploadFile" enctype="multipart/form-data">
    <table>
        <tr>
            <td><form:label path="file">Select a file to upload</form:label></td>
            <td><input type="file" name="file" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form>
```

업로드된 파일을 저장하기 위해 MultipartFile 변수를 사용할 수 있다.

컨트롤러 메서드 내부의 요청 매개변수에서 이 변수를 검색할 수 있다.

```java
@RequestMapping(value = "/uploadFile", method = RequestMethod.POST)
public String submit(@RequestParam("file") MultipartFile file, ModelMap modelMap) {
    modelMap.addAttribute("file", file);
    return "fileUploadView";
}
```

MultipartFile 클래스는 파일 이름, 파일 유형 등을 포함하여 업로드된 파일에 대한 세부 정보에 대한 액세스를 제공한다.

간단한 HTML 페이지를 사용하여 이 정보를 표시할 수 있다.

```html
<h2>Submitted File</h2>
<table>
    <tr>
        <td>OriginalFileName:</td>
        <td>${file.originalFilename}</td>
    </tr>
    <tr>
        <td>Type:</td>
        <td>${file.contentType}</td>
    </tr>
</table>
```

## 4. 여러 파일 업로드
단일 요청으로 여러 파일을 업로드하려면 양식에 여러 입력 파일 필드를 넣기만 하면 된다.

```html
<form:form method="POST" action="/spring-mvc-java/uploadMultiFile" enctype="multipart/form-data">
    <table>
        <tr>
            <td>Select a file to upload</td>
            <td><input type="file" name="files" /></td>
        </tr>
        <tr>
            <td>Select a file to upload</td>
            <td><input type="file" name="files" /></td>
        </tr>
        <tr>
            <td>Select a file to upload</td>
            <td><input type="file" name="files" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form:form>
```

MultipartFile의 배열로 액세스할 수 있도록 각 입력 필드가 동일한 이름을 갖도록 주의해야 한다.

```java
@RequestMapping(value = "/uploadMultiFile", method = RequestMethod.POST)
public String submit(@RequestParam("files") MultipartFile[] files, ModelMap modelMap) {
    modelMap.addAttribute("files", files);
    return "fileUploadView";
}
```

이제 해당 배열을 반복하여 파일 정보를 표시할 수 있다.

```java
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
    <head>
        <title>Spring MVC File Upload</title>
    </head>
    <body>
        <h2>Submitted Files</h2>
        <table>
            <c:forEach items="${files}" var="file">
                <tr>
                    <td>OriginalFileName:</td>
                    <td>${file.originalFilename}</td>
                </tr>
                <tr>
                    <td>Type:</td>
                    <td>${file.contentType}</td>
                </tr>
            </c:forEach>
        </table>
    </body>
</html>
```

## 5. 추가 양식 데이터가 있는 파일 업로드
업로드 중인 파일과 함께 서버에 추가 정보를 보낼 수도 있다.

양식에 필수 필드를 포함하기만 하면 된다.

```html
<form:form method="POST" action="/spring-mvc-java/uploadFileWithAddtionalData" enctype="multipart/form-data">
    <table>
        <tr>
            <td>Name</td>
            <td><input type="text" name="name" /></td>
        </tr>
        <tr>
            <td>Email</td>
            <td><input type="text" name="email" /></td>
        </tr>
        <tr>
            <td>Select a file to upload</td>
            <td><input type="file" name="file" /></td>
        </tr>
        <tr>
            <td><input type="submit" value="Submit" /></td>
        </tr>
    </table>
</form:form>
```

컨트롤러에서 `@RequestParam` 주석을 사용하여 모든 양식 데이터를 가져올 수 있다.

```java
@PostMapping("/uploadFileWithAddtionalData")
public String submit(
  @RequestParam MultipartFile file, @RequestParam String name,
  @RequestParam String email, ModelMap modelMap) {

    modelMap.addAttribute("name", name);
    modelMap.addAttribute("email", email);
    modelMap.addAttribute("file", file);
    return "fileUploadView";
}
```

JSTL 태그가 있는 HTML 페이지를 사용하여 정보를 표시할 수 있다.

또한 모델 클래스의 모든 양식 필드를 캡슐화하고 컨트롤러에서 `@ModelAttribute` 주석을 사용할 수 있다. 이것은 파일과 함께 많은 추가 필드가 있을 때 유용하다.

```java
public class FormDataWithFile {

    private String name;
    private String email;
    private MultipartFile file;

    // standard getters and setters
}
```

```java
@PostMapping("/uploadFileModelAttribute")
public String submit(@ModelAttribute FormDataWithFile formDataWithFile, ModelMap modelMap) {

    modelMap.addAttribute("formDataWithFile", formDataWithFile);
    return "fileUploadView";
}
```

## 6. 스프링 부트 파일 업로드
Spring Boot를 사용하고 있다면 지금까지 본 모든 것이 여전히 적용된다.

그러나 Spring Boot를 사용하면 번거로움 없이 모든 것을 훨씬 더 쉽게 구성하고 시작할 수 있다.

특히 의존성에 웹 모듈을 포함한다면 부트가 서블릿을 등록하고 구성할 것이기 때문에 어떤 서블릿도 구성할 필요가 없다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

최대 파일 업로드 크기를 제어하려면 `application.properties를` 편집할 수 있다.

```property
spring.servlet.multipart.max-file-size=128KB
spring.servlet.multipart.max-request-size=128KB
```

또한 파일 업로드 활성화 여부와 파일 업로드 위치를 제어할 수 있다.

```property
spring.servlet.multipart.enabled=true
spring.servlet.multipart.location=${java.io.tmpdir}
```

다른 운영 체제에 임시 위치를 사용할 수 있도록 `${java.io.tmpdir}`을 사용하여 업로드 위치를 정의했다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-file-upload](https://www.baeldung.com/spring-file-upload)
