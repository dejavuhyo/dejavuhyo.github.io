---
title: Spring RestTemplate
author: dejavuhyo
date: 2022-11-18 22:00:00 +0900
categories: [Application, Framework]
tags: [spring-resttemplate, resttemplate, rest-api, http-get, http-post, http-delete, http-put, resttemplate-특징, resttemplate-사용]
---

## 1. RestTemplate이란
Spring에서 지원하는 객체로 간편하게 Rest 방식 API를 호출할 수 있는 Spring 내장 클래스이다. Spring 3.0부터 지원되었고, json, xml 응답을 모두 받을 수 있다.

Rest API 서비스를 요청 후 응답받을 수 있도록 설계되어 있으며 HTTP 프로토콜의 메소드(ex. GET, POST, DELETE, PUT)들에 적합한 여러 메소드를 제공한다.

> Spring Framework 5부터는 WebFlux 스택과 함께 Spring은 WebClient라는 새로운 HTTP 클라이언트를 도입하여 기존의 동기식 API를 제공할 뿐만 아니라 효율적인 비차단 및 비동기 접근 방식을 지원하여, Spring 5.0 이후부터는 RestTemplate는 deprecated 되었다.

## 2. RestTemplate의 특징

* Spring 3.0부터 지원하는 Spring의 HTTP 통신 템플릿

* HTTP 요청 후 JSON, XML, String과 같은 응답을 받을 수 있는 템플릿

* Blocking I/O 기반의 동기방식을 사용하는 템플릿

* RESTful 형식에 맞추어진 템플릿

* Header, Content-Tpye등을 설정하여 외부 API 호출

* Server to Server 통신에 사용

## 3. RestTemplate 동작 원리

![resttemplate](/assets/img/2022-11-18-spring-resttemplate/resttemplate.png)

① 애플리케이션 내부에서 REST API에 요청하기 위해 RestTemplate의 메셔드릴 호출 한다.

② RestTemplate은 MessageConverter를 이용해 java object를 request body에 담을 message(JSON etc.)로 변환한다. 메시지 형태는 상황에 따라 다름

③ ClientHttpRequestFactory에서 ClientHttpRequest을 받아와 요청을 전달한다.

④ 실질적으로 ClientHttpRequest가 HTTP 통신으로 요청을 수행한다.

⑤ RestTemplate이 에러핸들링을 한다.

⑥ ClientHttpResponse에서 응답 데이터를 가져와 오류가 있으면 처리한다.

⑦ MessageConverter를 이용해 response body의 message를 java object로 변환한다.

⑧ 결과를 애플리케이션에 돌려준다.

## 4. 지원 메서드

| 메서드 | HTTP | 설명 |
|:-----:|:-----:|:-----:|
| getForObject : Object | GET | GET 방식 요청으로 결과를 객체로 반환 |
| getForEntity : ResponseEntity | GET | GET 방식 요청으로 결과를 ResponseEntity로 반환 |
| postForLocation | POST | POST 방식 요청으로 결과를 헤더에 저장된 URI로 반환 |
| postForObject : Object | POST | POST 방식 요청으로 결과를 객체로 반환 |
| postForEntity : ResponseEntity | POST | POST 방식 요청으로 결과를 ResponseEntity로 반환 |
| delete | DELETE | DELETE 방식 요청으로 메서드 실행 |
| headForHeaders | HEADER | 헤더의 정보를 얻을 수 있고, HTTP HEAD 메서드 사용 |
| put | PUT | HTTP PUT 메서드를 실행 |
| patchForObject | PATCH | HTTP PATCH 메서드를 실행 |
| optionsForAllow | OPTIONS | 지원하는 HTTP 메서드 조회 |
| exchange : ResponseEntity | any | 헤더 생성 및 어떤 요청이든 사용 가능 |
| execute | any | Request/Response 콜백을 수정할 수 있음 |

## 5. RestTemplate 사용

### 1) 의존성 추가

* Maven

```xml
<dependency>
   <groupId>org.apache.httpcomponents</groupId>
   <artifactId>httpcore</artifactId>
   <version>4.4.15</version>
</dependency>

<dependency>
   <groupId>org.apache.httpcomponents</groupId>
   <artifactId>httpclient</artifactId>
   <version>4.5.13</version>
</dependency>
```

* Gradle

```gradle
implementation 'org.apache.httpcomponents:httpcore:4.4.15'
implementation 'org.apache.httpcomponents:httpclient:4.5.13'
```

### 2) 사용예제

```java
@PostMapping(path = "/getInfo")
public String getInfo() {
   // request url
   String url = "";
   String jsonData = "{}";

   // create an instance of RestTemplate
   RestTemplate restTemplate = new RestTemplate();
   HttpHeaders headers = new HttpHeaders();
   MediaType mediaType = new MediaType("application", "json", Charset.forName("UTF-8"));
   headers.setContentType(mediaType);
   HttpEntity<String> entity = new HttpEntity<>(jsonData, headers);

   String response = restTemplate.postForObject(url, entity, String.class);
   System.out.println(response);

   return response;
}
```

## [출처 및 참고]
* [https://blog.naver.com/hj_kim97/222295259904](https://blog.naver.com/hj_kim97/222295259904)
* [https://stackoverflow.com/questions/4075991/post-request-via-resttemplate-in-json](https://stackoverflow.com/questions/4075991/post-request-via-resttemplate-in-json)
