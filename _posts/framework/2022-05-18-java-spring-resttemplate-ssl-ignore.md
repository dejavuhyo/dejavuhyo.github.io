---
title: Spring에서 RestTemplate, HttpClient 사용 시 SSL 유효성 검사 안 함 방법
author: dejavuhyo
date: 2022-05-18 21:40:00 +0900
categories: [Application, Framework]
tags: [spring-resttemplate-ssl-ignore, spring-httpclient-ssl-ignore, ssl-ignore, resttemplate-ssl-ignore, httpclient-ssl-ignore, ssl-유효성-검사-무시, 유효성-검사-무시]
---

## 1. 오류
API를 요청하는 Java의 신뢰하는 인증서 목록(keystore)에 사용하고자 하는 인증기관이 등록되어 있지 않아 나타나는 오류이다.

## 2. 해결방법
모든 인증서를 신뢰하는 방법과 인증서를 추가하는 방법이 있다.

모든 인증서를 신뢰하는 방법을 통해 해결해본다.

Local 환경에서 외부 API를 테스트하면서 개발할 때 주로 발생하기 때문에 Local 환경에서만 모든 인증서 신뢰하기를 적용하면 된다.

![error](/assets/img/2022-05-18-java-spring-resttemplate-ssl-ignore/error.png)

### 1) 의존성 추가

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.13</version>
</dependency>
```

### 2) Controller.java

```java
@GetMapping("/data")
public ResponseEntity data() throws KeyStoreException, NoSuchAlgorithmException, KeyManagementException {

    HttpClientBuilder httpClientBuilder = HttpClientBuilder.create();

    // 모든 인증서를 신뢰하도록 설정한다
    SSLContext sslContext = new SSLContextBuilder().loadTrustMaterial(null, (X509Certificate[] chain, String authType) -> true).build();
    httpClientBuilder.setSSLContext(sslContext);

    // Https 인증 요청시 호스트네임 유효성 검사를 진행하지 않게 한다.
    SSLConnectionSocketFactory sslSocketFactory = new SSLConnectionSocketFactory(sslContext, NoopHostnameVerifier.INSTANCE);
    Registry<ConnectionSocketFactory> socketFactoryRegistry = RegistryBuilder.<ConnectionSocketFactory>create()
            .register("http", PlainConnectionSocketFactory.getSocketFactory())
            .register("https", sslSocketFactory).build();

    PoolingHttpClientConnectionManager connMgr = new PoolingHttpClientConnectionManager(socketFactoryRegistry);
    httpClientBuilder.setConnectionManager(connMgr);

    // RestTemplate 와 HttpClient 연결
    HttpClient httpClient = httpClientBuilder.build();
    HttpComponentsClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory();
    requestFactory.setHttpClient(httpClient);

    RestTemplate restTemplate = new RestTemplate(requestFactory);

    // API 기본 인증 헤드 생성
    HttpHeaders headers = new HttpHeaders() {% raw %}{{
        String auth = "user:password";
        byte[] encodedAuth = Base64.encodeBase64(auth.getBytes(StandardCharsets.US_ASCII));

        String authHeader = "Basic " + new String(encodedAuth);
        set("Authorization", authHeader);
    }}{% endraw %};

    ResponseEntity<String> responseEntity = restTemplate.exchange("https://192.168.0.1:8080/data", HttpMethod.GET, new HttpEntity<>(headers), String.class);

    return new ResponseEntity(responseEntity, HttpStatus.OK);
}
```

## [출처 및 참고]
* [https://reference-m1.tistory.com/374](https://reference-m1.tistory.com/374)
* [https://gist.github.com/ageofsys/08408cd6e107d665762a30d5e22df0a5](https://gist.github.com/ageofsys/08408cd6e107d665762a30d5e22df0a5)
