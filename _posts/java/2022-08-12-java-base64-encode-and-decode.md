---
title: 자바 Base64 인코딩 및 디코딩
author: dejavuhyo
date: 2022-08-12 11:00:00 +0900
categories: [Language, Java]
tags: [java-base64, base64-encode, base64-decode, base64-encode-decode, java-encode-decode, base64, 자바-base64, 자바-인코딩, 자바-디코딩, base64-인코딩, base64-디코딩]
---

## 1. Base64용 자바 8
Java 8은 마침내 `java.util.Base64` 유틸리티 클래스를 통해 표준 API에 Base64 기능을 추가 했다.

### 1) 자바 8 기본 Base64
기본 인코더는 작업을 단순하게 유지하고 입력을 줄 분리 없이 있는 그대로 인코딩한다.

인코더는 입력을 `A-Za-z0-9+/` 문자 집합의 문자 집합에 매핑한다.

먼저 간단한 String을 인코딩해본다.

```java
String originalInput = "test input";
String encodedString = Base64.getEncoder().encodeToString(originalInput.getBytes());
```

간단한 `getEncoder()` 유틸리티 메소드를 통해 전체 Encoder API를 검색하는 방법에 유의한다.

이제 해당 문자열을 원래 형식으로 다시 디코딩한다.

```java
byte[] decodedBytes = Base64.getDecoder().decode(encodedString);
String decodedString = new String(decodedBytes);
```

### 2) 패딩 없는 Java 8 Base64 인코딩
Base64 인코딩에서 출력 인코딩된 문자열의 길이는 3의 배수여야 한다. 인코더는 이 요구 사항을 충족하기 위해 필요에 따라 출력 끝에 하나 또는 두 개의 패딩 문자(`=`)를 추가한다.

디코딩 시 디코더는 이러한 추가 패딩 문자를 버린다.

때로는 출력 패딩을 건너뛸 필요가 있다. 예를 들어 결과 문자열은 다시 디코딩되지 않는다. 따라서 패딩 없이 인코딩 하도록 선택할 수 있다.

```java
String encodedString = Base64.getEncoder().withoutPadding().encodeToString(originalInput.getBytes());
```

### 3) 자바 8 URL 인코딩
URL 인코딩은 기본 인코더와 매우 유사하다. 또한 URL 및 파일 이름 Safe Base64 알파벳을 사용한다. 또한 줄 구분을 추가하지 않는다.

```java
String originalUrl = "https://www.google.co.nz/?gfe_rd=cr&ei=dzbFV&gws_rd=ssl#q=java";
String encodedUrl = Base64.getUrlEncoder().encodeToString(originalURL.getBytes());
```

디코딩은 거의 동일한 방식으로 발생한다. `getUrlDecoder()` 유틸리티 메소드는 `java.util.Base64.Decoder`를 리턴한다 . 따라서 URL을 디코딩하는 데 사용한다.

```java
byte[] decodedBytes = Base64.getUrlDecoder().decode(encodedUrl);
String decodedUrl = new String(decodedBytes);
```

### 4) 자바 8 MIME 인코딩
인코딩할 몇 가지 기본 MIME 입력을 생성하여 시작한다.

```java
private static StringBuilder getMimeBuffer() {
    StringBuilder buffer = new StringBuilder();
    for (int count = 0; count < 10; ++count) {
        buffer.append(UUID.randomUUID().toString());
    }
    return buffer;
}
```

MIME 인코더는 기본 알파벳을 사용하여 Base64로 인코딩된 출력을 생성한다. 그러나 형식은 MIME 친화적이다.

출력의 각 줄은 76자를 넘지 않는다. 또한 캐리지 리턴과 줄 바꿈(`\r\n`)으로 끝난다.

```java
StringBuilder buffer = getMimeBuffer();
byte[] encodedAsBytes = buffer.toString().getBytes();
String encodedMime = Base64.getMimeEncoder().encodeToString(encodedAsBytes);
```

디코딩 프로세스에서 `java.util.Base64.Decoder`를 반환하는 `getMimeDecoder()` 메서드를 사용할 수 있다.

```java
byte[] decodedBytes = Base64.getMimeDecoder().decode(encodedMime);
String decodedMime = new String(decodedBytes);
```

## 2. Apache Commons Code를 사용한 인코딩/디코딩
먼저 pom.xml 에서 [commons-codec](https://search.maven.org/search?q=g:commons-codec%20AND%20a:commons-codec) 종속성 을 정의해야 한다.

```xml
<dependency>
    <groupId>commons-codec</groupId>
    <artifactId>commons-codec</artifactId>
    <version>1.15</version>
</dependency>
```

주요 API는 `org.apache.commons.codec.binary.Base64` 클래스이다. 다양한 생성자로 초기화할 수 있다.

* Base64(boolean urlSafe)는 URL 안전 모드(켜기 또는 끄기)를 제어하여 Base64 API를 생성한다.

* Base64(int lineLength)는 URL 안전하지 않은 모드에서 Base64 API를 생성하고 줄의 길이를 제어한다(기본값은 76).

* Base64(int lineLength, `byte[] lineSeparator)`는 기본적으로 CRLF(`\r\n`)인 추가 줄 구분자를 허용하여 Base64 API를 만든다.

Base64 API가 생성되면 인코딩과 디코딩이 모두 매우 간단하다.

```java
String originalInput = "test input";
Base64 base64 = new Base64();
String encodedString = new String(base64.encode(originalInput.getBytes()));
```

또한 Base64 클래스의 `decode()` 메서드는 디코딩된 문자열을 반환한다.

```java
String decodedString = new String(base64.decode(encodedString.getBytes()));
```

또 다른 옵션은 인스턴스를 생성하는 대신 Base64의 정적 API를 사용하는 것이다.

```java
String originalInput = "test input";
String encodedString = new String(Base64.encodeBase64(originalInput.getBytes()));
String decodedString = new String(Base64.decodeBase64(encodedString.getBytes()));
```

## 3. 문자열을 바이트 배열로 변환
때로는 String을 `byte[]`로 변환해야 한다. 가장 간단한 방법은 String `getBytes()` 메서드를 사용하는 것이다.

```java
String originalInput = "test input";
byte[] result = originalInput.getBytes();

assertEquals(originalInput.length(), result.length);
```

기본 인코딩에 의존하지 않고 인코딩도 제공할 수 있다. 결과적으로 시스템에 따라 다르다.

```java
String originalInput = "test input";
byte[] result = originalInput.getBytes(StandardCharsets.UTF_16);

assertTrue(originalInput.length() < result.length);
```

문자열이 Base64로 인코딩된 경우 Base64 디코더를 사용할 수 있다.

```java
String originalInput = "dGVzdCBpbnB1dA==";
byte[] result = Base64.getDecoder().decode(originalInput);

assertEquals("test input", new String(result));
```

DatatypeConverter `parseBase64Binary()` 메서드를 사용할 수도 있다.

```java
String originalInput = "dGVzdCBpbnB1dA==";
byte[] result = DatatypeConverter.parseBase64Binary(originalInput);

assertEquals("test input", new String(result));
```

마지막으로 `DatatypeConverter.parseHexBinary` 메서드를 사용하여 16진수 문자열을 `byte[]`로 변환할 수 있다.

```java
String originalInput = "7465737420696E707574";
byte[] result = DatatypeConverter.parseHexBinary(originalInput);

assertEquals("test input", new String(result));
```

## [출처 및 참고]
* <https://www.baeldung.com/java-base64-encode-and-decode>
