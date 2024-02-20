---
title: Java String을 Byte Array로 변환
author: dejavuhyo
date: 2024-02-20 17:02:00 +0900
categories: [Language, Java]
tags: [java-json, array-list, json-array]
---

## 1. String을 Byte Array로 변환
문자열은 Java에서 유니코드 문자 배열로 저장된다. 이를 바이트 배열로 변환하기 위해 문자 시퀀스를 바이트 시퀀스로 변환한다. [Charset](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/charset/Charset.html) 인스턴스를 사용한다. 이 클래스는 `char` 시퀀스와 `byte` 시퀀스 간의 매핑을 지정한다.

위의 프로세스를 `encoding`이라고 한다.

Java에서는 다양한 방법으로 문자열을 바이트 배열로 인코딩할 수 있다.

### 1) String.getBytes() 사용
String 클래스는 문자열을 바이트 배열로 인코딩하기 위해 오버로드된 세 가지 getBytes 메서드를 제공한다.

* [getBytes()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#getBytes(java.nio.charset.Charset)) - 플랫폼의 기본 문자 세트를 사용하여 인코딩한다.

* [getBytes(String charsetName)](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#getBytes(java.lang.String)) - 명명된 charset을 사용하여 인코딩한다.

* [getBytes(Charset charset)](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#getBytes(java.nio.charset.Charset)) - 제공된 charset을 사용하여 인코딩한다.

플랫폼의 기본 문자 집합을 사용하여 문자열을 인코딩 한다.

```java
String inputString = "Hello World!";
byte[] byteArrray = inputString.getBytes();
```

위의 방법은 플랫폼의 기본 문자 집합을 사용하므로 플랫폼에 따라 다르다. `Charset.defaultCharset()`을 호출하여 이 문자 세트를 얻을 수 있다.

그런 다음 명명된 문자 집합을 사용하여 문자열을 인코딩한다.

```java
@Test
public void whenGetBytesWithNamedCharset_thenOK() 
  throws UnsupportedEncodingException {
    String inputString = "Hello World!";
    String charsetName = "IBM01140";

    byte[] byteArrray = inputString.getBytes("IBM01140");
    
    assertArrayEquals(
      new byte[] { -56, -123, -109, -109, -106, 64, -26, -106, -103, -109, -124, 90 },
      byteArrray);
}
```

명명된 문자 집합이 지원되지 않는 경우 이 메서드는 UnsupportedEncodingException을 발생시킨다.

입력에 문자 세트에서 지원하지 않는 문자가 포함된 경우 위 두 버전의 동작은 정의되지 않는다. 대조적으로, 세 번째 버전은 지원되지 않는 입력을 인코딩하기 위해 charset의 기본 대체 바이트 배열을 사용한다.

다음으로 `getBytes()` 메서드의 세 번째 버전을 호출 하고 Charset 인스턴스를 전달한다.

```java
@Test
public void whenGetBytesWithCharset_thenOK() {
    String inputString = "Hello ਸੰਸਾਰ!";
    Charset charset = Charset.forName("ASCII");

    byte[] byteArrray = inputString.getBytes(charset);

    assertArrayEquals(
      new byte[] { 72, 101, 108, 108, 111, 32, 63, 63, 63, 63, 63, 33 },
      byteArrray);
}
```

여기서는 Charset 인스턴스를 얻기 위해 [Charset.forName](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/charset/Charset.html#forName(java.lang.String)) 팩토리 메소드를 사용하고 있다. 요청된 문자 집합의 이름이 유효하지 않은 경우 이 메서드는 런타임 예외를 발생시킨다. 또한 현재 JVM에서 문자 세트가 지원되는 경우 런타임 예외가 발생한다.

그러나 일부 문자 세트는 모든 Java 플랫폼에서 사용이 보장된다. StandardCharsets 클래스는 이러한 문자 세트에 대한 상수를 정의한다.

마지막으로 표준 문자 세트 중 하나를 사용하여 인코딩한다.

```java
@Test
public void whenGetBytesWithStandardCharset_thenOK() {
    String inputString = "Hello World!";
    Charset charset = StandardCharsets.UTF_16;

    byte[] byteArrray = inputString.getBytes(charset);

    assertArrayEquals(
      new byte[] { -2, -1, 0, 72, 0, 101, 0, 108, 0, 108, 0, 111, 0, 32, 0, 87, 0, 111, 0, 114, 0, 108, 0, 100, 0, 33 },
      byteArrray);
}
```

다양한 getBytes 버전에 대한 검토를 완료했다. 다음으로 Charset 자체에서 제공하는 메소드이다.

### 2) Charset.encode() 사용
Charset 클래스는 유니코드 문자를 바이트로 인코딩하는 편리한 메서드인 `encode()`를 제공한다. 이 방법은 항상 charset의 기본 대체 바이트 배열을 사용하여 잘못된 입력 및 매핑할 수 없는 문자를 대체한다.

encode 메소드를 사용하여 문자열을 바이트 배열로 변환한다.

```java
@Test
public void whenEncodeWithCharset_thenOK() {
    String inputString = "Hello ਸੰਸਾਰ!";
    Charset charset = StandardCharsets.US_ASCII;

    byte[] byteArrray = charset.encode(inputString).array();

    assertArrayEquals(
      new byte[] { 72, 101, 108, 108, 111, 32, 63, 63, 63, 63, 63, 33 },
      byteArrray);
}
```

위에서 볼 수 있듯이 지원되지 않는 문자는 문자 세트의 기본 대체 바이트 "63"으로 대체되었다.

### 3) CharsetEncoder
CharsetEncoder는 유니코드 문자를 지정된 charset에 대한 바이트 시퀀스로 변환한다. 또한 인코딩 프로세스를 세밀하게 제어할 수 있다.

이 클래스를 사용하여 문자열을 바이트 배열로 변환한다.

```java
@Test
public void whenUsingCharsetEncoder_thenOK()
  throws CharacterCodingException {
    String inputString = "Hello ਸੰਸਾਰ!";
    CharsetEncoder encoder = StandardCharsets.US_ASCII.newEncoder();
    encoder.onMalformedInput(CodingErrorAction.IGNORE)
      .onUnmappableCharacter(CodingErrorAction.REPLACE)
      .replaceWith(new byte[] { 0 });

    byte[] byteArrray = encoder.encode(CharBuffer.wrap(inputString))
                          .array();

    assertArrayEquals(
      new byte[] { 72, 101, 108, 108, 111, 32, 0, 0, 0, 0, 0, 33 },
      byteArrray);
}
```

여기서는 Charset 객체에서 newEncoder 메서드를 호출하여 CharsetEncoder의 인스턴스를 생성한다.

그런 다음 `onMalformedInput()` 및 `onUnmappableCharacter()` 메서드를 호출하여 오류 조건에 대한 작업을 지정한다. 다음 작업을 지정할 수 있다.

* IGNORE - 잘못된 입력을 삭제한다.

* REPLACE - 잘못된 입력을 교체한다.

* REPORT - CoderResult 객체를 반환하거나 CharacterCodingException을 발생시켜 오류를 보고한다.

또한, 대체 바이트 배열을 지정하기 위해 `replacementWith()` 메서드를 사용하고 있다.

## 2. Byte Array를 String으로 변환
바이트 배열을 문자열로 변환하는 과정을 `decoding`이라고 한다. `encoding`과 유사하게 이 프로세스에는 Charset이 필요하다.

그러나 바이트 배열을 디코딩하는데 문자 세트를 사용할 수는 없다. 특히, 문자열을 바이트 배열로 인코딩한 문자 세트를 사용해야 한다.

또한 다양한 방법으로 바이트 배열을 문자열로 변환할 수 있다.

### 1) String 생성자 사용
String 클래스에는 바이트 배열을 입력으로 사용하는 몇 가지 생성자가 있다. 모두 getBytes 메소드와 유사하지만 그 반대이다.

이제 플랫폼의 기본 문자 집합을 사용하여 바이트 배열을 문자열로 변환한다.

```java
@Test
public void whenStringConstructorWithDefaultCharset_thenOK() {
    byte[] byteArrray = { 72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100, 33 };
    
    String string = new String(byteArrray);
    
    assertNotNull(string);
}
```

여기서는 디코딩된 문자열의 내용에 대해 아무 것도 주장하지 않는다. 플랫폼의 기본 문자 세트에 따라 다른 것으로 디코딩될 수 있기 때문이다.

이러한 이유로 일반적으로 이 방법을 피해야 한다.

그런 다음 디코딩을 위해 명명된 문자 집합을 사용한다.

```java
@Test
public void whenStringConstructorWithNamedCharset_thenOK()
    throws UnsupportedEncodingException {
    String charsetName = "IBM01140";
    byte[] byteArrray = { -56, -123, -109, -109, -106, 64, -26, -106,
      -103, -109, -124, 90 };

    String string = new String(byteArrray, charsetName);
        
    assertEquals("Hello World!", string);
}
```

명명된 문자 집합을 JVM에서 사용할 수 없는 경우 이 메서드는 예외를 발생시킨다.

Charset 객체를 사용하여 디코딩을 수행한다.

```java
@Test
public void whenStringConstructorWithCharSet_thenOK() {
    Charset charset = Charset.forName("UTF-8");
    byte[] byteArrray = { 72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100, 33 };

    String string = new String(byteArrray, charset);

    assertEquals("Hello World!", string);
}
```

마지막으로 동일한 작업에 표준 Charset을 사용한다.

```java
@Test
public void whenStringConstructorWithStandardCharSet_thenOK() {
    Charset charset = StandardCharsets.UTF_16;
        
    byte[] byteArrray = { -2, -1, 0, 72, 0, 101, 0, 108, 0, 108, 0, 111, 0, 32, 0, 87, 0, 111, 0, 114, 0, 108, 0, 100, 0, 33 };

    String string = new String(byteArrray, charset);

    assertEquals("Hello World!", string);
}
```

### 2) Charset.decode() 사용
Charset 클래스는 ByteBuffer를 String으로 변환하는 `decode()` 메서드를 제공한다.

```java
@Test
public void whenDecodeWithCharset_thenOK() {
    byte[] byteArrray = { 72, 101, 108, 108, 111, 32, -10, 111,
      114, 108, -63, 33 };
    Charset charset = StandardCharsets.US_ASCII;
    String string = charset.decode(ByteBuffer.wrap(byteArrray))
                      .toString();

    assertEquals("Hello �orl�!", string);
}
```

여기서 유효하지 않은 입력은 문자 세트의 기본 대체 문자로 대체된다.

### 3) CharsetDecoder
내부적으로 디코딩하기 위한 이전의 모든 접근 방식은 [CharsetDecoder](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/charset/CharsetDecoder.html) 클래스를 사용한다. 디코딩 프로세스를 세밀하게 제어하기 위해 이 클래스를 직접 사용할 수 있다.

```java
@Test
public void whenUsingCharsetDecoder_thenOK()
  throws CharacterCodingException {
    byte[] byteArrray = { 72, 101, 108, 108, 111, 32, -10, 111, 114,
      108, -63, 33 };
    CharsetDecoder decoder = StandardCharsets.US_ASCII.newDecoder();

    decoder.onMalformedInput(CodingErrorAction.REPLACE)
      .onUnmappableCharacter(CodingErrorAction.REPLACE)
      .replaceWith("?");

    String string = decoder.decode(ByteBuffer.wrap(byteArrray))
                      .toString();

    assertEquals("Hello ?orl?!", string);
}
```

여기서는 유효하지 않은 입력과 지원되지 않는 문자를 "?"로 대체한다.

유효하지 않은 입력이 있는 경우 알림을 받으려면 디코더를 변경할 수 있다.

```java
decoder.onMalformedInput(CodingErrorAction.REPORT)
  .onUnmappableCharacter(CodingErrorAction.REPORT)
```

## [출처 및 참고]
* [https://www.baeldung.com/java-string-to-byte-array](https://www.baeldung.com/java-string-to-byte-array)
