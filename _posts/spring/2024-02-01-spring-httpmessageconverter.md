---
title: Spring Http Message Converters
author: dejavuhyo
date: 2024-02-01 16:41:00 +0900
categories: [Framework, Spring]
tags: [spring-message-converters, httpmessageconverter, message-converters, http-message, 스프링-메시지, 메시지-변환]
---

## 1. Basics

### 1) Enable Web MVC
웹 애플리케이션이 Spring MVC 지원으로 구성되어야 한다. 이를 수행하는 방법은 `@EnableWebMvc` 주석을 사용하는 것이다.

```java
@EnableWebMvc
@Configuration
@ComponentScan({ "com.baeldung.web" })
public class WebConfig implements WebMvcConfigurer {
    
    // ...
    
}
```

이 클래스는 HTTP 변환기의 기본 목록을 자체적으로 변경할 수 있는 WebMvcConfigurer를 구현한다.

### 2) 기본 메시지 변환기
기본적으로 다음 HttpMessageConverter 인스턴스는 미리 활성화되어 있다.

* ByteArrayHttpMessageConverter - 바이트 배열을 변환한다.

* StringHttpMessageConverter - 문자열을 변환한다.

* ResourceHttpMessageConverter - 모든 유형의 octet stream에 대해 `org.springframework.core.io.Resource`를 변환한다.

* SourceHttpMessageConverter - `javax.xml.transform.Source`를 변환한다.

* FormHttpMessageConverter - 양식 데이터를 `MultiValueMap<String, String>`에서 변환한다.

* Jaxb2RootElementHttpMessageConverter - Java 객체를 XML로 변환한다(JAXB2가 클래스 경로에 있는 경우에만 추가됨).

* MappingJackson2HttpMessageConverter - JSON을 변환한다(Jackson 2가 클래스 경로에 있는 경우에만 추가됨).

* MappingJacksonHttpMessageConverter - JSON을 변환한다(Jackson이 클래스 경로에 있는 경우에만 추가됨).

* AtomFeedHttpMessageConverter - Atom 피드를 변환한다(Rome이 클래스 경로에 있는 경우에만 추가됨).

* RssChannelHttpMessageConverter - RSS 피드를 변환한다(Rome이 클래스 경로에 있는 경우에만 추가됨).

## 2. JSON 전용 Client-Server 통신

### 1) High-Level 콘텐츠 협상
각 HttpMessageConverter 구현에는 하나 이상의 연관된 MIME 유형이 있다.

새로운 요청을 받으면 Spring은 "Accept" 헤더를 사용하여 응답해야 하는 미디어 유형을 결정한다.

그런 다음 해당 특정 미디어 유형을 처리할 수 있는 등록된 변환기를 찾으려고 한다. 마지막으로 이를 사용하여 엔터티를 변환하고 응답을 다시 보낸다.

JSON 정보가 포함된 요청을 수신하는 프로세스는 유사하다. 프레임워크는 "Content-Type" 헤더를 사용하여 요청 본문의 미디어 유형을 결정한다.

그런 다음 클라이언트가 보낸 본문을 Java 개체로 변환할 수 있는 HttpMessageConverter를 검색한다.

* 클라이언트는 모든 Foo 리소스를 JSON으로 가져오기 위해 Accept 헤더를 `application/json`으로 설정하여 `/foos`에 GET 요청을 보낸다.

* Foo Spring Controller가 적중되고 해당 Foo Java 엔터티를 반환한다.

* 그런 다음 Spring은 Jackson 메시지 변환기 중 하나를 사용하여 엔터티를 JSON으로 마샬링한다.

이것이 어떻게 작동하는지, 그리고 `@ResponseBody` 및 `@RequestBody` 주석을 어떻게 활용할 수 있는지 확인한다.

### 2) @ResponseBody
Controller 메서드의 `@ResponseBody`는 메서드의 반환 값이 HTTP 응답의 본문에 직접 직렬화됨을 Spring에 나타낸다. 클라이언트가 지정한 "Accept" 헤더는 엔터티를 마샬링하기 위한 적절한 Http 변환기를 선택하는데 사용된다.

```java
@GetMapping("/{id}")
public @ResponseBody Foo findById(@PathVariable long id) {
    return fooService.findById(id);
}
```

이제 클라이언트는 요청(예: curl command)에서 `application/json`에 "Accept" 헤더를 지정한다.

```shell
curl --header "Accept: application/json" 
  http://localhost:8080/spring-boot-rest/foos/1
```

Foo class이다.

```java
public class Foo {
    private long id;
    private String name;
}
```

HTTP Response Body이다. 

```json
{
    "id": 1,
    "name": "Paul",
}
```

### 3) @RequestBody
Controller 메소드의 인수에 `@RequestBody` 주석을 사용하여 HTTP 요청의 본문이 특정 Java 엔터티로 역직렬화되었음을 나타낼 수 있다. 적절한 변환기를 결정하기 위해 Spring은 클라이언트 요청의 "Content-Type" 헤더를 사용한다. 

```java
@PutMapping("/{id}")
public @ResponseBody void update(@RequestBody Foo foo, @PathVariable String id) {
    fooService.update(foo);
}
```

다음으로, "Content-Type"을 application/json으로 지정하여 JSON 개체와 함께 이를 사용한다.

```shell
curl -i -X PUT -H "Content-Type: application/json"  
-d '{"id":"83","name":"klik"}' http://localhost:8080/spring-boot-rest/foos/1
```

성공적인 응답인 200 OK를 받게 된다.

```text
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Content-Length: 0
Date: Fri, 10 Jan 2014 11:18:54 GMT
```

## 3. 사용자 정의 변환기 Configuration
WebMvcConfigurer 인터페이스를 구현하고 configureMessageConverters 메소드를 재정의하여 메시지 변환기를 사용자 정의 할 수도 있다.

```java
@EnableWebMvc
@Configuration
@ComponentScan({ "com.baeldung.web" })
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> messageConverters) {
        messageConverters.add(createXmlHttpMessageConverter());
        messageConverters.add(new MappingJackson2HttpMessageConverter());
    }

    private HttpMessageConverter<Object> createXmlHttpMessageConverter() {
        MarshallingHttpMessageConverter xmlConverter = new MarshallingHttpMessageConverter();

        XStreamMarshaller xstreamMarshaller = new XStreamMarshaller();
        xmlConverter.setMarshaller(xstreamMarshaller);
        xmlConverter.setUnmarshaller(xstreamMarshaller);

        return xmlConverter;
    } 
}
```

예제에서는 MarshallingHttpMessageConverter라는 새 변환기를 만들고 Spring XStream 지원을 사용하여 이를 구성한다. 이는 기본 마샬링 프레임워크(이 경우 XStream)의 하위 수준 API로 작업하고 원하는 대로 구성할 수 있기 때문에 상당한 유연성을 허용한다.

예에서는 XStream 라이브러리를 클래스 경로에 추가해야 한다.

또한 이 지원 클래스를 확장하면 이전에 사전 등록된 기본 메시지 변환기가 손실된다는 점에 유의한다.

물론 이제 자체 MappingJackson2HttpMessageConverter를 정의하여 Jackson에 대해서도 동일한 작업을 수행할 수 있다. 이 변환기에 사용자 정의 ObjectMapper를 설정 하고 필요에 따라 구성 할 수 있다.

이 경우 XStream은 선택된 marshaller/unmarshaller 구현이었지만 JibxMarshaller와 같은 다른 구현도 사용할 수 있다.

이 시점에서 백엔드에서 XML을 활성화하면 XML 표현과 함께 API를 사용할 수 있다.

```shell
curl --header "Accept: application/xml" 
  http://localhost:8080/spring-boot-rest/foos/1
```

### 1) 스프링 부트 지원
Spring Boot를 사용하는 경우 위에서 했던 것처럼 WebMvcConfigurer를 구현하고 모든 메시지 변환기를 수동으로 추가하는 것을 피할 수 있다.

컨텍스트에서 다양한 HttpMessageConverter 빈을 정의할 수 있으며 Spring Boot는 이를 자동 구성에 자동으로 추가한다.

```java
@Bean
public HttpMessageConverter<Object> createXmlHttpMessageConverter() {
    MarshallingHttpMessageConverter xmlConverter = new MarshallingHttpMessageConverter();

    // ...

    return xmlConverter;
}
```

## 4. HTTP 메시지 변환기와 함께 Spring의 RestTemplate 사용
서버 측뿐만 아니라 Spring RestTemplate의 클라이언트 측에서도 HTTP 메시지 변환을 구성할 수 있다.

적절한 경우 "Accept" 및 "Content-Type" 헤더를 사용하여 템플릿을 구성한다. 그런 다음 JSON과 XML을 모두 사용하여 Foo 리소스의 전체 마샬링 및 역마샬링을 통해 REST API를 사용한다.

### 1) Accept 헤더가 없는 리소스 검색

```java
@Test
public void whenRetrievingAFoo_thenCorrect() {
    String URI = BASE_URI + "foos/{id}";

    RestTemplate restTemplate = new RestTemplate();
    Foo resource = restTemplate.getForObject(URI, Foo.class, "1");

    assertThat(resource, notNullValue());
}
```

### 2) application/xml Accept 헤더를 사용하여 리소스 검색
이제 리소스를 XML 표현으로 명시적으로 검색한다. 변환기 세트를 정의하고 이를 RestTemplate에 설정한다.

XML을 사용하고 있으므로 이전과 동일한 XStream 마샬러를 사용한다.

```java
@Test
public void givenConsumingXml_whenReadingTheFoo_thenCorrect() {
    String URI = BASE_URI + "foos/{id}";

    RestTemplate restTemplate = new RestTemplate();
    restTemplate.setMessageConverters(getXmlMessageConverters());

    HttpHeaders headers = new HttpHeaders();
    headers.setAccept(Collections.singletonList(MediaType.APPLICATION_XML));
    HttpEntity<String> entity = new HttpEntity<>(headers);

    ResponseEntity<Foo> response = 
      restTemplate.exchange(URI, HttpMethod.GET, entity, Foo.class, "1");
    Foo resource = response.getBody();

    assertThat(resource, notNullValue());
}

private List<HttpMessageConverter<?>> getXmlMessageConverters() {
    XStreamMarshaller marshaller = new XStreamMarshaller();
    marshaller.setAnnotatedClasses(Foo.class);
    MarshallingHttpMessageConverter marshallingConverter = 
      new MarshallingHttpMessageConverter(marshaller);

    List<HttpMessageConverter<?>> converters = new ArrayList<>();
    converters.add(marshallingConverter);
    return converters;
}
```

### 3) application/json Accept 헤더를 사용하여 리소스 검색
마찬가지로 이제 JSON을 요청하여 REST API를 사용한다.

```java
@Test
public void givenConsumingJson_whenReadingTheFoo_thenCorrect() {
    String URI = BASE_URI + "foos/{id}";

    RestTemplate restTemplate = new RestTemplate();
    restTemplate.setMessageConverters(getJsonMessageConverters());

    HttpHeaders headers = new HttpHeaders();
    headers.setAccept(Collections.singletonList(MediaType.APPLICATION_JSON));
    HttpEntity<String> entity = new HttpEntity<String>(headers);

    ResponseEntity<Foo> response = 
      restTemplate.exchange(URI, HttpMethod.GET, entity, Foo.class, "1");
    Foo resource = response.getBody();

    assertThat(resource, notNullValue());
}

private List<HttpMessageConverter<?>> getJsonMessageConverters() {
    List<HttpMessageConverter<?>> converters = new ArrayList<>();
    converters.add(new MappingJackson2HttpMessageConverter());
    return converters;
}
```

### 4) XML Content-Type으로 리소스 업데이트
마지막으로 JSON 데이터를 REST API로 보내고 Content-Type 헤더를 통해 해당 데이터의 미디어 유형을 지정한다.

```java
@Test
public void givenConsumingXml_whenWritingTheFoo_thenCorrect() {
    String URI = BASE_URI + "foos";
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.setMessageConverters(getJsonAndXmlMessageConverters());

    Foo resource = new Foo("jason");
    HttpHeaders headers = new HttpHeaders();
    headers.setAccept(Collections.singletonList(MediaType.APPLICATION_JSON));
    headers.setContentType((MediaType.APPLICATION_XML));
    HttpEntity<Foo> entity = new HttpEntity<>(resource, headers);

    ResponseEntity<Foo> response = 
      restTemplate.exchange(URI, HttpMethod.POST, entity, Foo.class);
    Foo fooResponse = response.getBody();

    assertThat(fooResponse, notNullValue());
    assertEquals(resource.getName(), fooResponse.getName());
}

private List<HttpMessageConverter<?>> getJsonAndXmlMessageConverters() {
    List<HttpMessageConverter<?>> converters = getJsonMessageConverters();
    converters.addAll(getXmlMessageConverters());
    return converters;
}
```

미디어 유형을 혼합할 수 있다. XML 데이터를 보내고 있지만 서버에서 JSON 데이터가 다시 오기를 기다리고 있다. 이는 Spring 변환 메커니즘이 실제로 얼마나 강력한지를 보여준다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-httpmessageconverter-rest](https://www.baeldung.com/spring-httpmessageconverter-rest)
