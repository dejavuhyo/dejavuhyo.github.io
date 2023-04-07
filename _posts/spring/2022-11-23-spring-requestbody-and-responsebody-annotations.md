---
title: Spring RequestBody와 ResponseBody Annotations
author: dejavuhyo
date: 2022-11-23 20:00:00 +0900
categories: [Framework, Spring]
tags: [requestbody, responsebody, requestbody-responsebody, requestbody-annotations, responsebody-annotations, java-객체-역직열화, java-객체-직열화]
---

## 1. @RequestBody
간단히 말해 `@RequestBody` 주석은 HttpRequest 본문을 전송 또는 도메인 객체에 매핑 하여 인바운드 HttpRequest 본문을 Java 객체로 자동 역직렬화할 수 있도록 한다.

Spring 컨트롤러 메서드이다.

```java
@PostMapping("/request")
public ResponseEntity postController(
  @RequestBody LoginForm loginForm) {

    exampleService.fakeAuthenticate(loginForm);
    return ResponseEntity.ok(HttpStatus.OK);
}
```

Spring은 적절한 JSON이 지정된 경우 자동으로 Java 유형으로 역직렬화됩니다.

기본적으로 `@RequestBody` 주석을 사용하여 주석을 다는 유형은 클라이언트 측 컨트롤러에서 보낸 JSON과 일치해야 한다.

```java
public class LoginForm {
    private String username;
    private String password;
    // ...
}
```

여기서 HttpRequest 본문을 나타내는데 사용하는 개체는 LoginForm 개체에 매핑된다.

CURL을 사용하여 이를 테스트한다.

```shell
curl -i \
-H "Accept: application/json" \
-H "Content-Type:application/json" \
-X POST --data
  '{"username": "johnny", "password": "password"}' "https://localhost:8080/.../request"
```

이것이 `@RequestBody` 주석을 사용하는 Spring REST API 및 Angular 클라이언트에 필요한 전부이다.

## 2. @ResponseBody
`@ResponseBody` 주석은 반환된 개체가 자동으로 JSON으로 직렬화되고 HttpResponse 개체로 다시 전달된다는 것을 컨트롤러에 알려준다.

사용자 지정 Response 개체가 있다고 가정 한다.

```java
public class ResponseTransfer {
    private String text;

    // standard getters/setters
}
```

다음으로 관련 컨트롤러를 구현할 수 있다.

```java
@Controller
@RequestMapping("/post")
public class ExamplePostController {

    @Autowired
    ExampleService exampleService;

    @PostMapping("/response")
    @ResponseBody
    public ResponseTransfer postResponseController(
      @RequestBody LoginForm loginForm) {
        return new ResponseTransfer("Thanks For Posting!!!");
     }
}
```

브라우저의 개발자 콘솔이나 Postman과 같은 도구를 사용하면 다음과 같은 응답을 볼 수 있다.

```json
{"text":"Thanks For Posting!!!"}
```

Spring에서는 기본적으로 `@ResponseBody` 주석을 사용하므로 `@RestController` 주석 컨트롤러에 주석을 달 필요가 없다.

### 1) 콘텐츠 유형 설정
`@ResponseBody` 주석을 사용할 때 메소드가 반환하는 내용 유형을 여전히 명시적으로 설정할 수 있다.

이를 위해 @RequestMapping의 products 속성을 사용할 수 있다. `@PostMapping`, `@GetMapping` 등의 주석은 해당 매개 변수에 대한 별칭을 정의한다.

JSON 응답을 보내는 새 endpoint를 추가한다.

```java
@PostMapping(value = "/content", produces = MediaType.APPLICATION_JSON_VALUE)
@ResponseBody
public ResponseTransfer postResponseJsonContent(
  @RequestBody LoginForm loginForm) {
    return new ResponseTransfer("JSON Content!");
}
```

예제에서는 `MediaType.APPLICATION_JSON_VALUE` 상수를 사용했다. 또는 `application/json` 을 직접 사용할 수 있다.

다음으로, 동일한 `/content` 경로에 매핑되지만 대신 XML 콘텐츠를 반환하는 새로운 방법이다.

```java
@PostMapping(value = "/content", produces = MediaType.APPLICATION_XML_VALUE)
@ResponseBody
public ResponseTransfer postResponseXmlContent(
  @RequestBody LoginForm loginForm) {
    return new ResponseTransfer("XML Content!");
}
```

이제 요청의 헤더에 전송된 Accept 매개 변수의 값에 따라 다른 응답을 받는다.

다음과 같이 실행해 본다.

```shell
curl -i \
-H "Accept: application/json" \
-H "Content-Type:application/json" \
-X POST --data
  '{"username": "johnny", "password": "password"}' "https://localhost:8080/.../content"
```

CURL 명령은 JSON 응답을 반환한다.

```json
HTTP/1.1 200
Content-Type: application/json
Transfer-Encoding: chunked
Date: Thu, 20 Feb 2020 19:43:06 GMT

{"text":"JSON Content!"}
```

Accept 매개변수를 변경해 본다.

```shell
curl -i \
-H "Accept: application/xml" \
-H "Content-Type:application/json" \
-X POST --data
  '{"username": "johnny", "password": "password"}' "https://localhost:8080/.../content"
```

이번에는 XML 콘텐츠를 얻는다.

```xml
HTTP/1.1 200
Content-Type: application/xml
Transfer-Encoding: chunked
Date: Thu, 20 Feb 2020 19:43:19 GMT

<ResponseTransfer><text>XML Content!</text></ResponseTransfer>
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-request-response-body](https://www.baeldung.com/spring-request-response-body)
