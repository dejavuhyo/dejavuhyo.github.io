---
title: Spring JAX-RS의 CORS
author: dejavuhyo
date: 2024-04-05 13:39:00 +0900
categories: [Framework, Spring]
tags: [spring-rest-api, spring, jax-rs, jax-rs-cors, cors]
---

## 1. CORS 메커니즘 활성화 방법
JAX-RS에서 CORS를 활성화하는 방법에는 두 가지가 있다. 첫 번째이자 가장 기본적인 방법은 모든 요청에서 런타임에 필요한 응답 헤더를 삽입하는 필터를 만드는 것이다. 다른 하나는 각 URL 끝점에 적절한 헤더를 수동으로 추가하는 것이다.

이상적으로는 첫 번째 솔루션을 사용해야 한다. 그러나 이것이 옵션이 아닌 경우에는 더 많은 수동 옵션도 기술적으로 괜찮다.

### 1) 필터 사용
JAX-RS에는 컨테이너 응답 필터로 구현되는 [ContainerResponseFilter](https://docs.oracle.com/javaee/7/api/javax/ws/rs/container/ContainerResponseFilter.html) 인터페이스가 있다. 일반적으로 이 필터 인스턴스는 모든 HTTP 응답에 전역적으로 적용된다.

이 인터페이스를 구현하여 나가는 요청마다 `Access-Control-Allow-*` 헤더를 삽입하고 CORS 메커니즘을 활성화하는 사용자 정의 필터를 생성한다.

```java
@Provider
public class CorsFilter implements ContainerResponseFilter {

    @Override
    public void filter(ContainerRequestContext requestContext, 
      ContainerResponseContext responseContext) throws IOException {
          responseContext.getHeaders().add(
            "Access-Control-Allow-Origin", "*");
          responseContext.getHeaders().add(
            "Access-Control-Allow-Credentials", "true");
          responseContext.getHeaders().add(
           "Access-Control-Allow-Headers",
           "origin, content-type, accept, authorization");
          responseContext.getHeaders().add(
            "Access-Control-Allow-Methods", 
            "GET, POST, PUT, DELETE, OPTIONS, HEAD");
    }
}
```

여기에 몇 가지 요점이 있다.

* ContainerResponseFilter를 구현하는 필터는 JAX-RS 런타임에서 검색할 수 있도록 `@Provider`로 명시적으로 주석을 달아야 한다.

* `Access-Control-Allow-*` 헤더에 `*`를 삽입한다. 이는 이 서버 인스턴스에 대한 모든 URL 끝점이 모든 도메인을 통해 액세스될 수 있음을 의미한다. 도메인 간 액세스를 명시적으로 제한하려면 이 헤더에 해당 도메인을 언급해야 한다.

### 2) 각 Endpoint에 헤더 수정 사용
앞서 설명한 것처럼 엔드포인트 수준에서도 `Access-Control-Allow-*` 헤더를 명시적으로 삽입할 수 있다.

```java
@GET
@Path("/")
@Produces({MediaType.TEXT_PLAIN})
public Response index() {
    return Response
      .status(200)
      .header("Access-Control-Allow-Origin", "*")
      .header("Access-Control-Allow-Credentials", "true")
      .header("Access-Control-Allow-Headers",
        "origin, content-type, accept, authorization")
      .header("Access-Control-Allow-Methods", 
        "GET, POST, PUT, DELETE, OPTIONS, HEAD")
      .entity("")
      .build();
}
```

여기서 주목해야 할 점은 대규모 애플리케이션에서 CORS를 활성화하려는 경우 이 방법을 시도해서는 안 된다는 것이다. 이 경우 추가 오버헤드가 발생할 수 있는 모든 URL 끝점에 헤더를 수동으로 삽입해야 하기 때문이다.

그러나 이 기술은 일부 URL 끝점에서만 CORS를 활성화해야 하는 애플리케이션에서 사용할 수 있다 .

### 3) 테스트
애플리케이션이 실행되면 curl 명령을 사용하여 헤더를 테스트할 수 있다. 샘플 헤더 출력은 다음과 같아야 한다.

```text
HTTP/1.1 200 OK
Date : Tue, 13 May 2014 12:30:00 GMT
Connection : keep-alive
Access-Control-Allow-Origin : *
Access-Control-Allow-Credentials : true
Access-Control-Allow-Headers : origin, content-type, accept, authorization
Access-Control-Allow-Methods : GET, POST, PUT, DELETE, OPTIONS, HEAD
Transfer-Encoding : chunked
```

또한 간단한 AJAX 함수를 생성하고 도메인 간 기능을 확인할 수 있다.

```javascript
function call(url, type, data) {
    var request = $.ajax({
      url: url,
      method: "GET",
      data: (data) ? JSON.stringify(data) : "",
      dataType: type
    });
 
    request.done(function(resp) {
      console.log(resp);
    });
 
    request.fail(function(jqXHR, textStatus) {
      console.log("Request failed: " + textStatus);
    });
};
```

물론 실제로 검사를 수행하려면 사용하는 API가 아닌 다른 출처에서 이를 실행해야 한다.

포트가 원본을 결정하기 때문에 별도의 포트에서 클라이언트 앱을 실행하면 로컬에서 매우 쉽게 수행할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/cors-in-jax-rs](https://www.baeldung.com/cors-in-jax-rs)
