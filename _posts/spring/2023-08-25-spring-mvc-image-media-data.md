---
title: Spring MVC 이미지/미디어 데이터 반환
author: dejavuhyo
date: 2023-08-25 07:40:00 +0900
categories: [Framework, Spring]
tags: [spring-image, spring-media, spring-data, 스프링-이미지, 스피링-미디어]
---

## 1. HttpServletResponse 사용
이미지 다운로드의 가장 기본적인 접근 방식은 응답 객체에 대해 직접 작업하고 순수 서블릿 구현을 모방하는 것이다.

```java
@RequestMapping(value = "/image-manual-response", method = RequestMethod.GET)
public void getImageAsByteArray(HttpServletResponse response) throws IOException {
    InputStream in = servletContext.getResourceAsStream("/WEB-INF/images/image-example.jpg");
    response.setContentType(MediaType.IMAGE_JPEG_VALUE);
    IOUtils.copy(in, response.getOutputStream());
}
```

다음 요청을 실행하면 브라우저에서 이미지가 렌더링된다.

```text
http://localhost:8080/spring-mvc-xml/image-manual-response.jpg
```

`org.apache.commons.io` 패키지의 IOUtils 덕분에 구현이 매우 간단하고 간단하다. 그러나 이 접근 방식의 단점은 잠재적인 변화에 대해 견고하지 않다는 것이다. MIME 유형은 하드 코딩되어 있으며 변환 논리를 변경하거나 이미지 위치를 외부화하려면 코드를 변경해야 한다.

## 2. HttpMessageConverter 사용
Spring MVC 프레임워크의 메시지 변환 및 콘텐츠 협상 기능을 활용하지 않는 기본 접근 방식을 부트스트랩하려면 다음을 수행해야 합니다.

* `@ResponseBody` 주석으로 컨트롤러 메서드에 주석을 단다.

* 컨트롤러 메서드의 반환 유형을 기반으로 적절한 메시지 변환기를 등록합니다(예를 들어 바이트 배열을 이미지 파일로 올바르게 변환하는데 필요한 ByteArrayHttpMessageConverter).

### 1) Configuration
변환기 구성을 보여주기 위해 메서드가 `byte[]` 유형을 반환할 때마다 메시지를 변환하는 내장 ByteArrayHttpMessageConverter를 사용한다.

ByteArrayHttpMessageConverter는 기본적으로 등록되지만 구성은 다른 기본 제공 또는 사용자 지정 변환기와 유사하다.

메시지 변환기 Bean을 적용하려면 Spring MVC 컨텍스트 내에 적절한 MessageConverter Bean을 등록하고 처리해야 하는 미디어 유형을 설정해야 한다. `<mvc:message-converters>` 태그를 사용하여 XML을 통해 정의할 수 있다.

이 태그는 다음 예와 같이 `<mvc:annotation-driven>` 태그 내에 정의되어야 한다.

```xml
<mvc:annotation-driven>
    <mvc:message-converters>
        <bean class="org.springframework.http.converter.ByteArrayHttpMessageConverter">
            <property name="supportedMediaTypes">
                <list>
                    <value>image/jpeg</value>
                    <value>image/png</value>
                </list>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

구성 부분에서는 `image/jpeg` 및 `image/png` 응답 콘텐츠 유형에 대해 ByteArrayHttpMessageConverter를 등록한다. `<mvc:message-converters>` 태그가 mvc 구성에 없으면 기본 변환기 세트가 등록된다.

또한 Java 구성을 사용하여 메시지 변환기를 등록할 수 있다.

```java
@Override
public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
    converters.add(byteArrayHttpMessageConverter());
}

@Bean
public ByteArrayHttpMessageConverter byteArrayHttpMessageConverter() {
    ByteArrayHttpMessageConverter arrayHttpMessageConverter = new ByteArrayHttpMessageConverter();
    arrayHttpMessageConverter.setSupportedMediaTypes(getSupportedMediaTypes());
    return arrayHttpMessageConverter;
}

private List<MediaType> getSupportedMediaTypes() {
    List<MediaType> list = new ArrayList<MediaType>();
    list.add(MediaType.IMAGE_JPEG);
    list.add(MediaType.IMAGE_PNG);
    list.add(MediaType.APPLICATION_OCTET_STREAM);
    return list;
}
```

### 2) Implementation
미디어 요청을 처리하는 메서드를 구현할 수 있다. 컨트롤러 메서드에 `@ResponseBody` 주석을 표시하고 `byte[]`를 반환 유형으로 사용해야 한다.

```java
@RequestMapping(value = "/image-byte-array", method = RequestMethod.GET)
public @ResponseBody byte[] getImageAsByteArray() throws IOException {
    InputStream in = servletContext.getResourceAsStream("/WEB-INF/images/image-example.jpg");
    return IOUtils.toByteArray(in);
}
```

메서드를 테스트하려면 브라우저에서 다음 요청을 실행한다.

```text
http://localhost:8080/spring-mvc-xml/image-byte-array.jpg
```

장점은 이 메서드가 HttpServletResponse에 대해 전혀 모르고 사용 가능한 변환기를 사용하는 것부터 사용자 지정 변환기를 지정하는 것까지 변환 프로세스를 고도로 구성할 수 있다는 것이다. 응답의 콘텐츠 유형은 하드 코딩할 필요가 없으며 요청 경로 접미사 `.jpg`를 기반으로 협상 된다.

이 접근 방식의 단점은 데이터 소스(로컬 파일, 외부 저장소 등)에서 이미지를 검색하기 위한 논리를 명시적으로 구현해야 하며 응답의 헤더나 상태 코드를 제어할 수 없다는 것이다.

## 3. ResponseEntity 클래스 사용
Response Entity에 래핑된 `byte[]`로 이미지를 반환할 수 있다. Spring MVC ResponseEntity는 HTTP 응답의 본문뿐만 아니라 헤더와 응답 상태 코드에 대한 제어를 가능하게 한다. 이 접근 방식에 따라 메서드의 반환 유형을 `ResponseEntity<byte[]>`로 정의하고 메서드 본문에 반환하는 ResponseEntity 개체를 생성 해야 한다.

```java
@RequestMapping(value = "/image-response-entity", method = RequestMethod.GET)
public ResponseEntity<byte[]> getImageAsResponseEntity() {
    HttpHeaders headers = new HttpHeaders();
    InputStream in = servletContext.getResourceAsStream("/WEB-INF/images/image-example.jpg");
    byte[] media = IOUtils.toByteArray(in);
    headers.setCacheControl(CacheControl.noCache().getHeaderValue());
    
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(media, headers, HttpStatus.OK);
    return responseEntity;
}
```

ResponseEntity를 사용하면 특정 요청에 대한 응답 코드를 구성할 수 있다.

응답 코드를 명시적으로 설정하는 것은 이미지를 찾을 수 없거나(FileNotFoundException) 손상된 경우(IOException) 예외 이벤트가 발생한 경우 특히 유용하다. 이러한 경우 필요한 것은 적절한 catch 블록에 `new ResponseEntity<>(null, headers, HttpStatus.NOT_FOUND)`와 같은 응답 코드를 설정하는 것뿐이다.

또한 응답에 특정 헤더를 설정해야 하는 경우 이 접근 방식은 메서드에서 매개 변수로 허용하는 HttpServletResponse 개체를 통해 헤더를 설정하는 것보다 더 간단하다. 이는 메소드 서명을 명확하고 집중적으로 만든다.

## 4. 리소스 클래스를 사용하여 이미지 반환
Resource 개체 형식으로 이미지를 반환할 수 있다.

Resource 인터페이스는 하위 수준 리소스에 대한 액세스를 추상화하기 위한 인터페이스이다. 이는 표준 `java.net.URL` 클래스를 보다 효과적으로 대체하기 위해 Spring에 도입되었다. 이를 통해 명시적으로 검색하는 코드를 작성할 필요 없이 다양한 유형의 리소스(로컬 파일, 원격 파일, 클래스 경로 리소스)에 쉽게 액세스할 수 있다.

이 접근 방식을 사용하려면 메서드의 반환 유형을 Resource로 설정해야 하며 `@ResponseBody` 주석으로 메서드에 주석을 달아야 한다.

### 1) Implementation

```java
@ResponseBody
@RequestMapping(value = "/image-resource", method = RequestMethod.GET)
public Resource getImageAsResource() {
   return new ServletContextResource(servletContext, "/WEB-INF/images/image-example.jpg");
}
```

또는 응답 헤더에 대해 더 많은 제어를 원하는 경우이다.

```java
@RequestMapping(value = "/image-resource", method = RequestMethod.GET)
@ResponseBody
public ResponseEntity<Resource> getImageAsResource() {
    HttpHeaders headers = new HttpHeaders();
    Resource resource = 
      new ServletContextResource(servletContext, "/WEB-INF/images/image-example.jpg");
    return new ResponseEntity<>(resource, headers, HttpStatus.OK);
}
```

이 접근 방식을 사용하면 이미지를 ResourceLoader 인터페이스 구현을 사용하여 로드할 수 있는 리소스로 처리할 수 있다. 이러한 경우 이미지의 정확한 위치를 추상화하면 ResourceLoader가 이미지 가 로드되는 위치를 결정한다.

구성을 사용하여 이미지 위치를 제어하고 파일 로딩 코드를 작성할 필요가 없도록 하는 일반적인 접근 방식을 제공한다.

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-image-media-data](https://www.baeldung.com/spring-mvc-image-media-data)
