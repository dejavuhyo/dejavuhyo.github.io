---
title: Spring MVC 콘텐츠 협상
author: dejavuhyo
date: 2023-08-22 05:55:00 +0900
categories: [Framework, Spring]
tags: [spring-content-negotiation, content-negotiation, 콘텐츠-협상]
---

## 1. 콘텐츠 협상 전략
일반적으로 요청의 미디어 유형을 결정하는 세 가지 옵션이 있다.

* (사용되지 않음) 요청에 URL 접미사(확장자) 사용(예: `.xml`/`.json`)

* 요청에 URL 매개변수 사용(예: `?format=json`)

* 요청에 Accept 헤더 사용

기본적으로 이것은 Spring 콘텐츠 협상 관리자가 이 세 가지 전략을 사용하려고 시도하는 순서이다. 그리고 이들 중 어느 것도 활성화되지 않은 경우 기본 콘텐츠 유형에 대한 폴백을 지정할 수 있다.

필요한 종속성을 추가한다. JSON 및 XML 표현으로 작업하고 있으므로 JSON용 Jackson을 사용한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.10.2</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.10.2</version>
</dependency>
```

XML 지원을 위해 JAXB, XStream 또는 최신 Jackson-XML 지원을 사용할 수 있다.

## 2. URL 접미사 전략
Spring Boot 2.6.x 버전에서는 등록된 Spring MVC 처리기 매핑에 대해 요청 경로를 일치시키는 기본 전략이 AntPathMatcher에서 PathPatternParser로 변경 되었다.

접미사 패턴 일치는 PathPatternParser에서 지원되지 않으므로 먼저 레거시 경로 일치자를 사용해야 한다.

`application.properties` 파일에 `spring.mvc.pathmatch.matching-strategy`를 추가하여 기본값을 AntPathMatcher로 다시 전환할 수 있다.

기본적으로 이 전략은 비활성화되어 있으며 application.properties에서 `spring.mvc.pathmatch.use-suffix-pattern`를 true로 설정하여 활성화해야 한다.

```property
spring.mvc.pathmatch.use-suffix-pattern=true
spring.mvc.pathmatch.matching-strategy=ant-path-matcher
```

활성화되면 프레임워크는 URL에서 바로 경로 확장자를 확인하여 출력 콘텐츠 유형을 결정할 수 있다.

일반적인 Spring 컨트롤러에는 다음과 같은 간단한 API 메서드 구현이 있다.

```java
@RequestMapping(
  value = "/employee/{id}", 
  produces = { "application/json", "application/xml" }, 
  method = RequestMethod.GET)
public @ResponseBody Employee getEmployeeById(@PathVariable long id) {
    return employeeMap.get(id);
}
```

리소스의 미디어 유형을 지정하기 위해 JSON 확장을 사용하여 호출한다.

```shell
curl http://localhost:8080/spring-mvc-basics/employee/10.json
```

JSON 확장을 사용하면 다음과 같은 결과를 얻을 수 있다.

```json
{
    "id": 10,
    "name": "Test Employee",
    "contactNumber": "999-999-9999"
}
```

그리고 다음은 요청-응답이 XML로 표시되는 모습이다.

```shell
curl http://localhost:8080/spring-mvc-basics/employee/10.xml
```

응답 본문이다.

```xml
<employee>
    <contactNumber>999-999-9999</contactNumber>
    <id>10</id>
    <name>Test Employee</name>
</employee>
```

이제 확장을 사용하지 않거나 구성되지 않은 확장을 사용하면 기본 콘텐츠 유형이 반환된다.

```shell
curl http://localhost:8080/spring-mvc-basics/employee/10
```

### 1) 자바 구성

```java
public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    configurer.favorPathExtension(true).
    favorParameter(false).
    ignoreAcceptHeader(true).
    useJaf(false).
    defaultContentType(MediaType.APPLICATION_JSON); 
}
```

먼저 경로 확장 전략을 사용하도록 설정한다. 또한 Spring Framework 5.2.4 부터는 컨텐츠 협상을 위한 경로 확장의 사용을 막기 위해 favorPathExtension(boolean) 메소드가 더 이상 사용되지 않는다.

그런 다음 콘텐츠 유형을 결정하는 경로 확장 방식에만 의존하기를 원하기 때문에 URL 매개 변수 전략과 Accept 헤더 전략을 비활성화한다.

그런 다음 Java Activation Framework를 끈다. JAF는 들어오는 요청이 우리가 구성한 전략과 일치하지 않는 경우 출력 형식을 선택하는 폴백 메커니즘으로 사용할 수 있다. JSON을 기본 콘텐츠 유형으로 구성할 것이기 때문에 비활성화한다. `useJaf()` 메서드는 Spring Framework 5부터 더 이상 사용되지 않는다.

마지막으로 JSON을 기본값으로 설정한다. 즉, 두 전략 중 어느 것도 일치하지 않으면 들어오는 모든 요청이 JSON을 제공하는 컨트롤러 메서드에 매핑된다.

### 2) XML 구성
XML만 사용하여 정확히 동일한 구성이다.

```xml
<bean id="contentNegotiationManager" 
  class="org.springframework.web.accept.ContentNegotiationManagerFactoryBean">
    <property name="favorPathExtension" value="true" />
    <property name="favorParameter" value="false"/>
    <property name="ignoreAcceptHeader" value="true" />
    <property name="defaultContentType" value="application/json" />
    <property name="useJaf" value="false" />
</bean>
```

## 3. URL 매개변수 전략
경로 매개변수를 사용하도록 Spring MVC를 설정한다.

favorParameter 속성 값을 true로 설정하여 이 전략을 활성화할 수 있다.

```shell
curl http://localhost:8080/spring-mvc-basics/employee/10?mediaType=json
```

JSON 응답 본문은 다음과 같다.

```json
{
    "id": 10,
    "name": "Test Employee",
    "contactNumber": "999-999-9999"
}
```

XML 매개변수를 사용하면 출력이 XML 형식이 된다.

```shell
curl http://localhost:8080/spring-mvc-basics/employee/10?mediaType=xml
```

응답 본문이다.

```xml
<employee>
    <contactNumber>999-999-9999</contactNumber>
    <id>10</id>
    <name>Test Employee</name>
</employee>
```

### 1) 자바 구성

```java
public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    configurer.favorPathExtension(false).
    favorParameter(true).
    parameterName("mediaType").
    ignoreAcceptHeader(true).
    useJaf(false).
    defaultContentType(MediaType.APPLICATION_JSON).
    mediaType("xml", MediaType.APPLICATION_XML). 
    mediaType("json", MediaType.APPLICATION_JSON); 
}
```

먼저 경로 확장 및 Accept 헤더 전략이 비활성화된다(JAF 포함).

나머지 구성은 동일하다.

### 2) XML 구성

```xml
<bean id="contentNegotiationManager" 
  class="org.springframework.web.accept.ContentNegotiationManagerFactoryBean">
    <property name="favorPathExtension" value="false" />
    <property name="favorParameter" value="true"/>
    <property name="parameterName" value="mediaType"/>
    <property name="ignoreAcceptHeader" value="true" />
    <property name="defaultContentType" value="application/json" />
    <property name="useJaf" value="false" />

    <property name="mediaTypes">
        <map>
            <entry key="json" value="application/json" />
            <entry key="xml" value="application/xml" />
        </map>
    </property>
</bean>
```

또한 두 전략(확장 및 매개변수)을 동시에 활성화할 수 있다.

```java
public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    configurer.favorPathExtension(true).
    favorParameter(true).
    parameterName("mediaType").
    ignoreAcceptHeader(true).
    useJaf(false).
    defaultContentType(MediaType.APPLICATION_JSON).
    mediaType("xml", MediaType.APPLICATION_XML). 
    mediaType("json", MediaType.APPLICATION_JSON); 
}
```

이 경우 Spring은 경로 확장자를 먼저 찾고, 확장자가 없으면 경로 매개변수를 찾는다. 입력 요청에서 이 두 가지를 모두 사용할 수 없는 경우 기본 콘텐츠 유형이 다시 반환된다.

## 4. Accept 헤더 전략
Accept 헤더가 활성화 되면 Spring MVC는 수신 요청에서 해당 값을 찾아 표현 유형을 결정한다.

이 접근 방식을 사용하려면 ignoreAcceptHeader 값을 false로 설정해야 하며 Accept 헤더에만 의존한다는 것을 알기 위해 다른 두 가지 전략을 비활성화한다.

### 1) 자바 구성

```java
public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    configurer.favorPathExtension(true).
    favorParameter(false).
    parameterName("mediaType").
    ignoreAcceptHeader(false).
    useJaf(false).
    defaultContentType(MediaType.APPLICATION_JSON).
    mediaType("xml", MediaType.APPLICATION_XML). 
    mediaType("json", MediaType.APPLICATION_JSON); 
}
```

### 2) XML 구성

```xml
<bean id="contentNegotiationManager" 
  class="org.springframework.web.accept.ContentNegotiationManagerFactoryBean">
    <property name="favorPathExtension" value="true" />
    <property name="favorParameter" value="false"/>
    <property name="parameterName" value="mediaType"/>
    <property name="ignoreAcceptHeader" value="false" />
    <property name="defaultContentType" value="application/json" />
    <property name="useJaf" value="false" />

    <property name="mediaTypes">
        <map>
            <entry key="json" value="application/json" />
            <entry key="xml" value="application/xml" />
        </map>
    </property>
</bean>
```

마지막으로 전체 구성에 플러그인하여 콘텐츠 협상 관리자를 켜야 한다.

```xml
<mvc:annotation-driven content-negotiation-manager="contentNegotiationManager" />
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-content-negotiation-json-xml](https://www.baeldung.com/spring-mvc-content-negotiation-json-xml)
