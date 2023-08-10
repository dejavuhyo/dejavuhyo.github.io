---
title: Spring Static Resources
author: dejavuhyo
date: 2023-08-11 07:20:00 +0900
categories: [Framework, Spring]
tags: [spring-static-resources, static-resources, spring-resources]
---

## 1. 스프링 부트 사용
Spring Boot는 정적 리소스 제공을 용이하게 하기 위해 미리 구성된 [ResourceHttpRequestHandler](https://github.com/spring-projects/spring-framework/blob/main/spring-webmvc/src/main/java/org/springframework/web/servlet/resource/ResourceHttpRequestHandler.java) 구현과 함께 제공된다.

기본적으로 이 핸들러는 클래스 경로에 있는 `/static`, `/public`, `/resources` 및 `/META-INF/resources` 디렉토리의 정적 콘텐츠를 제공한다. `src/main/resources`는 일반적으로 기본적인 클래스 경로에 있기 때문에 이러한 디렉토리를 거기에 둘 수 있다.

예를 들어 클래스 경로의 `/static` 디렉토리에 `about.html` 파일을 넣으면 `http://localhost:8080/about.html`을 통해 해당 파일에 액세스할 수 있다. 마찬가지로 언급된 다른 디렉토리에 해당 파일을 추가하여 동일한 결과를 얻을 수 있다.

### 1) 사용자 지정 경로 패턴
기본적으로 Spring Boot는 요청의 루트 부분인 `/**` 아래에 있는 모든 정적 콘텐츠를 제공한다. 좋은 기본 구성인 것처럼 보이지만 `spring.mvc.static-path-pattern` 구성 속성을 통해 변경할 수 있다.

예를 들어 `http://localhost:8080/content/about.html`을 통해 동일한 파일에 액세스하려는 경우 `application.properties`에서 변경할 수 있다.

```property
spring.mvc.static-path-pattern=/content/**
```

WebFlux 환경에서는 [spring.webflux.static-path-pattern](https://github.com/spring-projects/spring-boot/blob/c71ed407e43e561333719573d63464ae9db388c1/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/reactive/WebFluxProperties.java#L38) 속성을 사용해야 한다.

### 2) 맞춤 디렉토리
경로 패턴과 마찬가지로 [spring.web.resources.static-locations](https://github.com/spring-projects/spring-boot/blob/main/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/web/WebProperties.java#L86) 구성 속성을 통해 기본 리소스 위치를 변경할 수도 있다. 이 속성은 쉼표로 구분된 여러 리소스 위치를 허용할 수 있다.

```property
spring.web.resources.static-locations=classpath:/files/,classpath:/static-files
```

여기서 classpath 내부의 `/files` 및 `/static-files` 디렉토리에서 정적 콘텐츠를 제공하고 있다. 또한 Spring Boot는 클래스 경로 외부에서 정적 파일을 제공할 수 있다.

```property
spring.web.resources.static-locations=file:/opt/files
```

여기서는 [파일 리소스 서명](https://docs.spring.io/spring-framework/docs/5.2.4.RELEASE/spring-framework-reference/core.html#resources-resourceloader)인 `file:/`을 사용하여 로컬 디스크에서 파일을 제공한다.

## 2. XML 구성
XML 기반 구성으로 구식 방식을 사용해야 하는 경우 `mvc:resources` 요소를 잘 활용하여 특정 공용 URL 패턴으로 리소스의 위치를 ​​가리킬 수 있다.

예를 들어, 다음 줄은 `/resources/**`와 같은 공개 URL 패턴으로 들어오는 리소스에 대한 모든 요청을 애플리케이션의 루트 폴더 아래에 있는 `/resources/` 디렉토리에서 검색하여 제공한다.

```xml
<mvc:resources mapping="/resources/**" location="/resources/" />
```

이제 다음 HTML 페이지와 같은 CSS 파일에 액세스할 수 있다.

```html
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
    <link href="<c:url value="/resources/myCss.css" />" rel="stylesheet">
    <title>Home</title>
</head>
<body>
    <h1>Hello world!</h1>
</body>
</html>
```

## 3. ResourceHttpRequestHandler
Spring 3.1. 클래스 경로, WAR 또는 파일 시스템에서 정적 리소스를 제공하기 위해 ResourceHttpRequestHandler를 구성하기 위해 ResourceHand lerRegistry를 도입했다. 웹 컨텍스트 구성 클래스 내에서 프로그래밍 방식으로 ResourceHandlerRegistry를 구성할 수 있다.

### 1) WAR에 저장된 리소스 제공
이를 설명하기 위해 이전과 동일한 URL을 사용하여 `myCss.css` 를 가리키지 만 이제 실제 파일은 WAR의 `webapp/resources` 폴더에 위치하며 Spring 3.1+ 애플리케이션을 배포할 때 정적 리소스를 배치해야 한다.

```java
@Configuration
@EnableWebMvc
public class MvcConfig implements WebMvcConfigurer {
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry
          .addResourceHandler("/resources/**")
          .addResourceLocations("/resources/");	
    }
}
```

먼저 리소스 핸들러를 정의하여 외부 연결 URI 경로를 구성한다. 그런 다음 리소스가 실제로 위치한 물리적 경로에 내부적으로 해당 외부 URI 경로를 매핑한다.

물론 이 간단하면서도 유연한 API를 사용하여 여러 리소스 핸들러를 정의할 수 있다.

이제 html 페이지의 다음 줄은 `webapp/resources` 디렉토리 내의 `myCss.css` 리소스를 가져온다.

```xml
<link href="<c:url value="/resources/myCss.css" />" rel="stylesheet">
```

### 2) 파일 시스템에 저장된 리소스 제공
`/files/**` 패턴과 일치하는 공개 URL에 대한 요청이 들어올 때마다 `/opt/files/` 디렉토리에 저장된 리소스를 제공하고 싶다고 가정한다. URL 패턴을 구성하고 디스크의 특정 위치에 매핑하기만 하면 된다.

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry
      .addResourceHandler("/files/**")
      .addResourceLocations("file:/opt/files/");
}
```

Windows 사용자의 경우 이 예제에서 addResourceLocations에 전달된 인수는 `file:///C:/opt/files/`이다.

리소스 위치를 구성하면 `home.html`의 매핑된 URL 패턴을 사용하여 파일 시스템에 저장된 이미지를 로드 할 수 있다.

```html
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
    <link href="<c:url value="/resources/myCss.css" />" rel="stylesheet">
    <title>Home</title>
</head>
<body>
    <h1>Hello world!</h1>
    <img alt="image"  src="<c:url value="files/myImage.png" />">
</body>
</html>
```

### 3) 리소스에 대한 여러 위치 구성
둘 이상의 위치에서 리소스를 찾으려면 addResourceLocations 메서드를 사용하여 여러 위치를 포함할 수 있다. 리소스를 찾을 때까지 위치 목록이 순서대로 검색된다.

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry
      .addResourceHandler("/resources/**")
      .addResourceLocations("/resources/","classpath:/other-resources/");
}
```

다음 curl 요청은 애플리케이션의 `webappp/resources` 또는 classpath의 other-resources 폴더에 저장된 `Hello.html` 페이지를 표시한다.

```shell
curl -i http://localhost:8080/handling-spring-static-resources/resources/Hello.html
```

## 4. 새로운 ResourceResolver
Spring 4.1. 새로운 ResourcesResolvers와 함께 정적 리소스를 로드할 때 브라우저 성능을 최적화하는데 사용할 수 있는 다양한 유형의 리소스 확인자를 제공한다. 이러한 리졸버는 요청 처리를 최적화하기 위해 브라우저에 연결 및 캐시될 수 있다.

### 1) PathResourceResolver
이것은 가장 간단한 리졸버이며 그 목적은 공개 URL 패턴이 주어진 리소스를 찾는 것이다. 실제로 `ResourceChainRegistration`에 `ResourceResolver`를 추가하지 않으면 이것이 기본 확인자이다.

예이다.

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry
      .addResourceHandler("/resources/**")
      .addResourceLocations("/resources/","/other-resources/")
      .setCachePeriod(3600)
      .resourceChain(true)
      .addResolver(new PathResourceResolver());
}
```

> 주의 사항
>
> * 리소스 체인의 PathResourceResolver를 유일한 ResourceResolver로 등록하고 있다. 4.3 참조한다. 둘 이상의 ResourceResolver를 연결하는 방법을 확인한다.
>
> * 제공된 리소스는 3600초 동안 브라우저에 캐시된다.
>
> * 체인은 최종적으로 `resourceChain(true)` 메서드로 구성된다.

PathResourceResolver와 함께 `webapp/resources` 또는 `webapp/other-resources` 폴더 에서 `foo.js` 스크립트를 찾는 HTML 코드의 경우이다.

```html
<script type="text/javascript" src="<c:url value="/resources/foo.js" />">
```

### 2) EncodedResourceResolver
이 방법은 `Accept-Encoding` 요청 헤더 값을 기반으로 인코딩된 리소스를 찾으려고 시도한다.

예를 들어 gzip 콘텐츠 코딩을 사용하여 압축된 버전의 정적 리소스를 제공하여 대역폭을 최적화해야 할 수 있다.

EncodedResourceResolver를 구성하려면 PathResourceResolver를 구성한 것처럼 ResourceChain에서 구성해야 한다.

```java
registry
  .addResourceHandler("/other-files/**")
  .addResourceLocations("file:/Users/Me/")
  .setCachePeriod(3600)
  .resourceChain(true)
  .addResolver(new EncodedResourceResolver());
```

기본적으로 EncodedResourceResolver는 br 및 gzip 코딩을 지원하도록 구성된다.

따라서 다음 curl 요청은 파일 시스템의 `Users/Me/` 디렉터리에 있는 압축된 버전의 `Home.html` 파일을 가져온다.

```shell
curl -H  "Accept-Encoding:gzip" 
  http://localhost:8080/handling-spring-static-resources/other-files/Hello.html
```

헤더의 `Accept-Encoding` 값을 gzip으로 설정하는 방법에 주의한다. 이 특정 리졸버는 gzip 콘텐츠가 응답에 유효한 경우에만 실행되기 때문에 중요하다.

마지막으로 이전과 동일하게 압축된 버전은 브라우저에 캐시된 기간(이 경우 3600초) 동안 사용 가능한 상태로 유지된다.

### 3) ResourceResolver 연결 
리소스 조회를 최적화하기 위해 ResourceResolvers는 리소스 처리를 다른 확인자에게 위임할 수 있다. 체인에 위임할 수 없는 유일한 리졸버는 체인 끝에 추가해야 하는 PathResourceResolver 이다.

실제로 resourceChain이 true로 설정되지 않은 경우 기본적으로 리소스를 제공하는데 PathResourceResolver만 사용된다. 여기서 GzipResourceResolver가 실패한 경우 리소스를 해결하기 위해 PathResourceResolver를 연결하고 있다.

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry
      .addResourceHandler("/js/**")
      .addResourceLocations("/js/")
      .setCachePeriod(3600)
      .resourceChain(true)
      .addResolver(new GzipResourceResolver())
      .addResolver(new PathResourceResolver());
}
```

ResourceHandler에 `/js/**` 패턴을 추가했으므로 이제 `home.html` 페이지의 `webapp/js/` 디렉토리에 있는 `foo.js` 리소스를 포함시킨다.

```html
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
    <link href="<c:url value="/resources/bootstrap.css" />" rel="stylesheet" />
    <script type="text/javascript"  src="<c:url value="/js/foo.js" />"></script>
    <title>Home</title>
</head>
<body>
    <h1>This is Home!</h1>
    <img alt="bunny hop image"  src="<c:url value="files/myImage.png" />" />
    <input type = "button" value="Click to Test Js File" onclick = "testing();" />
</body>
</html>
```

Spring Framework 5.1부터 GzipResourceResolver가 EncodedResourceResolver를 위해 더 이상 사용되지 않는다. 따라서 앞으로는 사용을 자제해야 한다.

## 5. 추가 보안 구성
Spring Security를 ​​사용하는 경우 정적 리소스에 대한 액세스를 허용하는 것이 중요하다. 리소스 URL에 액세스하려면 해당 권한을 추가해야 한다.

```html
<intercept-url pattern="/files/**" access="permitAll" />
<intercept-url pattern="/other-files/**/" access="permitAll" />
<intercept-url pattern="/resources/**" access="permitAll" />
<intercept-url pattern="/js/**" access="permitAll" />
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-static-resources](https://www.baeldung.com/spring-mvc-static-resources)
