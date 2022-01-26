---
title: Java URL 인코딩/디코딩
author: dejavuhyo
date: 2022-01-13 10:10:00 +0900
categories: [Language, Java]
tags: [java-url-encoding-decoding, java-url-encoding, java-url-decoding, url-encoding-decoding, encoding-decoding, url-encoding, url-decoding, encoding, decoding, 자바-url-인코딩-디코딩, 자바-인코딩-디코딩, 인코딩, 디코딩, url-인코딩, url-디코딩, 인코딩-디코딩]
---

## 1. URL 인코딩/디코딩
URL 인코딩은 URL의 특수 문자를 사양을 준수하고 올바르게 이해하고 해석할 수 있는 표현으로 변환한다.

## 2. URL 분석
기본 URI 구문은 다음과 같이 일반화할 수 있다.

```text
scheme:[//[user:password@]host[:port]][/]path[?query][#fragment]
```

URI 인코딩의 첫 번째 단계는 해당 부분을 검사한 다음 관련 부분만 인코딩하는 것이다.

URI의 예이다.

```java
String testUrl = "http://www.baeldung.com?key1=value+1&key2=value%40%21%242&key3=value%253";
```

URI를 분석하는 한 가지 방법은 String 표현을 java.net.URI 클래스에 로드하는 것이다.

```java
@Test
public void givenURL_whenAnalyze_thenCorrect() throws Exception {
    URI uri = new URI(testUrl);

    assertThat(uri.getScheme(), is("http"));
    assertThat(uri.getHost(), is("www.baeldung.com"));
    assertThat(uri.getRawQuery(),
      .is("key1=value+1&key2=value%40%21%242&key3=value%253"));
}
```

URI 클래스는 문자열 표현 URL을 구문 분석하고 간단한 API를 통해 해당 부분을 표시한다.

## 3. URL 인코딩
URI를 인코딩할 때 일반적인 함정 중 하나는 전체 URI를 인코딩하는 것이다. 일반적으로 URI의 쿼리 부분만 인코딩하면 된다.

URLEncoder 클래스의 encode(data, encodingScheme) 메서드를 사용하여 데이터를 인코딩한다.

```java
private String encodeValue(String value) {
    return URLEncoder.encode(value, StandardCharsets.UTF_8.toString());
}

@Test
public void givenRequestParam_whenUTF8Scheme_thenEncode() throws Exception {
    Map<String, String> requestParams = new HashMap<>();
    requestParams.put("key1", "value 1");
    requestParams.put("key2", "value@!$2");
    requestParams.put("key3", "value%3");

    String encodedURL = requestParams.keySet().stream()
      .map(key -> key + "=" + encodeValue(requestParams.get(key)))
      .collect(joining("&", "http://www.baeldung.com?", ""));

    assertThat(testUrl, is(encodedURL));
```

인코딩 방법은 두 가지 매개변수를 허용한다.

* data: 번역할 문자열

* encodingScheme: 문자 인코딩의 이름

이 인코딩 방법은 문자열을 [application/x-www-form-urlencoded](https://www.w3.org/TR/html401/interact/forms.html#h-17.13.4.1) 형식으로 변환한다.

인코딩 체계는 특수 문자를 `%xy` 형식으로 표시되는 8비트의 2자리 16진수 표현으로 변환한다. 경로 매개변수를 처리하거나 동적 매개변수를 추가할 때 데이터를 인코딩한 다음 서버로 보낸다.

__참고:__ World Wide Web Consortium 권장 사항은 UTF-8을 사용해야 한다고 명시하고 있다. 그렇지 않으면 비호환성이 발생할 수 있다. (https://docs.oracle.com/javase/7/docs/api/java/net/URLEncoder.html)

## 4. URL 디코딩
이제 URLDecoder의 디코드 메서드를 사용하여 이전 URL을 디코딩 한다.

```java
private String decode(String value) {
    return URLDecoder.decode(value, StandardCharsets.UTF_8.toString());
}

@Test
public void givenRequestParam_whenUTF8Scheme_thenDecodeRequestParams() {
    URI uri = new URI(testUrl);

    String scheme = uri.getScheme();
    String host = uri.getHost();
    String query = uri.getRawQuery();

    String decodedQuery = Arrays.stream(query.split("&"))
      .map(param -> param.split("=")[0] + "=" + decode(param.split("=")[1]))
      .collect(Collectors.joining("&"));

    assertEquals(
      "http://www.baeldung.com?key1=value 1&key2=value@!$2&key3=value%3",
      scheme + "://" + host + "?" + decodedQuery);
}
```

여기서 중요한 두 부분은 다음과 같다.

* 디코딩하기 전에 URL 분석

* 인코딩 및 디코딩에 동일한 인코딩 체계 사용

분석보다 디코딩하는 경우 URL 부분이 올바르게 구문 분석되지 않을 수 있다. 다른 인코딩 체계를 사용하여 데이터를 디코딩하면 가비지 데이터가 생성된다.

## 5. 경로 세그먼트 인코딩
URLEncoder는 URL의 경로 세그먼트를 인코딩하는데 사용할 수 없다. 경로 구성 요소는 디렉토리 경로를 나타내는 계층 구조를 참조하거나 `/`로 구분된 리소스를 찾는 역할을 한다.

경로 세그먼트의 예약 문자가 쿼리 매개변수 값과 다르다. 예를 들어 `+` 기호는 경로 세그먼트에서 유효한 문자이므로 인코딩해서는 안된다.

경로 세그먼트를 인코딩하기 위해 대신 Spring Framework에서 UriUtils 클래스를 사용한다. UriUtils 클래스는 각각 경로 및 경로 세그먼트를 인코딩하기 위한 encodePath 및 encodePathSegment 메소드를 제공한다.

예이다.

```java
private String encodePath(String path) {
    try {
        path = UriUtils.encodePath(path, "UTF-8");
    } catch (UnsupportedEncodingException e) {
        LOGGER.error("Error encoding parameter {}", e.getMessage(), e);
    }
    return path;
}
```

```java
@Test
public void givenPathSegment_thenEncodeDecode() 
  throws UnsupportedEncodingException {
    String pathSegment = "/Path 1/Path+2";
    String encodedPathSegment = encodePath(pathSegment);
    String decodedPathSegment = UriUtils.decode(encodedPathSegment, "UTF-8");
    
    assertEquals("/Path%201/Path+2", encodedPathSegment);
    assertEquals("/Path 1/Path+2", decodedPathSegment);
}
```

위의 코드 스니펫에서 우리가 encodePathSegment 메소드를 사용할 때 인코딩된 값을 반환했고 `+`는 경로 구성 요소의 값 문자이기 때문에 인코딩되지 않는다는 것을 알 수 있다.

테스트 URL에 경로 변수를 추가해 본다.

```java
String testUrl = "/path+1?key1=value+1&key2=value%40%21%242&key3=value%253";
```

또한 적절하게 인코딩된 URL을 조합하고 주장하기 위해 섹션 2부터 테스트를 변경할 수 있다.

```java
String path = "path+1";
String encodedURL = requestParams.keySet().stream()
  .map(k -> k + "=" + encodeValue(requestParams.get(k)))
  .collect(joining("&", "/" + encodePath(path) + "?", ""));
assertThat(testUrl, CoreMatchers.is(encodedURL));
```

## [출처 및 참고]
* <https://www.baeldung.com/java-char-encoding>
