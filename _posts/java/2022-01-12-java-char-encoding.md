---
title: Java 문자 인코딩
author: dejavuhyo
date: 2022-01-12 10:20:00 +0900
categories: [Language, Java]
tags: [java-char-encoding, char-encoding, java-encoding, encoding, 자바-문자-인코딩, 자바-인코딩, 문자-인코딩, 인코딩]
---

## 1. 문자 인코딩의 중요성
우리는 종종 라틴어나 아랍어와 같은 다양한 쓰기 스크립트로 여러 언어에 속하는 텍스트를 처리해야 한다. 모든 언어의 모든 문자는 어떻게든 1과 0의 집합에 매핑되어야 한다. 컴퓨터가 우리의 모든 언어를 올바르게 처리할 수 있다는 것은 정말 놀라운 일이다.

이를 제대로 수행하려면 문자 인코딩에 대해 생각할 필요가 있다. 그렇게 하지 않으면 종종 데이터 손실과 보안 취약성이 발생할 수 있다.

이것을 더 잘 이해하기 위해 Java에서 텍스트를 디코딩하는 방법을 정의한다.

```java
String decodeText(String input, String encoding) throws IOException {
    return 
      new BufferedReader(
        new InputStreamReader(
          new ByteArrayInputStream(input.getBytes()), 
          Charset.forName(encoding)))
        .readLine();
}
```

여기에서 제공하는 입력 텍스트는 기본 플랫폼 인코딩을 사용한다.

이 방법을 "The façade pattern is a software design pattern."으로 입력 하여 실행하면 "US-ASCII"로 인코딩 하면 다음과 같이 출력된다.

```text
The fa��ade pattern is a software design pattern.
```

기대했던 것과는 다르다.

## 2. 기본
자세히 알아보기 전에 encoding, charsets 및 code point라는 세 가지 용어를 검토한다.

### 1) Encoding
컴퓨터는 1과 0과 같은 이진수 표현만 이해할 수 있다. 다른 내용을 처리하려면 실제 텍스트에서 이진 표현으로 매핑해야 한다. 이 매핑은 문자 인코딩 또는 단순히 인코딩으로 알고 있는 것이다.

예를 들어, US-ASCII에서 메시지 "T"의 첫 번째 문자는 "01010100"으로 인코딩된다.

### 2) Charsets
이진 표현에 대한 문자 매핑은 포함하는 문자에 따라 크게 다를 수 있다. 매핑에 포함된 문자의 수는 실제 사용에서 소수에서 모든 문자까지 다양할 수 있다. 매핑 정의에 포함된 문자 집합을 공식적으로 charset 이라고 한다.

예를 들어 [ASCII는 128자의 문자 집합](http://ee.hawaii.edu/~tep/EE160/Book/chap4/subsection2.1.1.1.html)이 있다.

### 3) Code Point
코드 포인트는 실제 인코딩에서 문자를 분리하는 추상화이다. 코드 포인트는 특정 문자 정수 기준이다.

정수 자체를 일반 10진수 또는 16진수 또는 8진수와 같은 대체 기수로 나타낼 수 있다. 큰 수를 쉽게 참조할 수 있도록 대체 기수를 사용한다.

예를 들어, 유니코드의 메시지 T의 첫 번째 문자에는 코드 포인트 `U+0054`(또는 십진수로 84)가 있다.

## 3. 인코딩 방식의 이해
문자 인코딩은 인코딩하는 문자 수에 따라 다양한 형태를 취할 수 있다.

인코딩된 문자 수는 일반적으로 바이트 수로 측정되는 각 표현의 길이와 직접적인 관계가 있다. 인코딩할 문자가 더 많다는 것은 본질적으로 더 긴 이진 표현이 필요하다는 것을 의미한다.

오늘날 실제로 사용되는 몇 가지 인기 있는 인코딩 체계이다.

### 1) Single-Byte Encoding
ASCII (American Standard Code for Information Exchange)라고 하는 초기 인코딩 체계 중 하나는 단일 바이트 인코딩 체계를 사용한다. 이것은 본질적으로 ASCII의 각 문자가 7비트 이진수로 표현 된다는 것을 의미한다. 이것은 여전히 모든 바이트에 1비트를 남겨둔다.

ASCII의 128자 세트는 소문자와 대문자의 영어 알파벳, 숫자, 일부 특수 문자 및 제어 문자를 다룬다.

특정 인코딩 체계에서 문자에 대한 이진 표현을 표시하는 간단한 방법을 Java로 정의한다.

```java
String convertToBinary(String input, String encoding) 
      throws UnsupportedEncodingException {
    byte[] encoded_input = Charset.forName(encoding)
      .encode(input)
      .array();  
    return IntStream.range(0, encoded_input.length)
        .map(i -> encoded_input[i])
        .mapToObj(e -> Integer.toBinaryString(e ^ 255))
        .map(e -> String.format("%1$" + Byte.SIZE + "s", e).replace(" ", "0"))
        .collect(Collectors.joining(" "));
}
```

이제 문자 'T'는 US-ASCII에서 84의 코드 포인트를 갖는다(자바에서는 ASCII를 US-ASCII라고 함).

유틸리티 메서드를 사용하면 이진 표현을 볼 수 있다.

```java
assertEquals(convertToBinary("T", "US-ASCII"), "01010100");
```

이것은 문자 'T'에 대한 7비트 이진 표현이다.

원래 ASCII는 사용되지 않은 모든 바이트의 최상위 비트를 남겼다. 동시에 ASCII는 특히 영어가 아닌 언어의 경우 상당히 많은 문자를 표현하지 못했다.

이로 인해 사용되지 않은 비트를 활용하고 128자를 추가로 포함시키려는 노력이 필요했다.

시간이 지남에 따라 제안되고 채택된 ASCII 인코딩 방식에는 여러 가지 변형이 있었다. 이들은 "ASCII 확장"으로 불린다.

많은 ASCII 확장은 다른 수준의 성공을 거두었지만 분명히 많은 문자가 아직 표현되지 않기 때문에 널리 채택되기에는 충분하지 않았다.

가장 널리 사용되는 ASCII 확장 중 하나는 "ISO Latin 1"이라고도 하는 ISO-8859-1 이다.

### 2) Multi-Byte Encoding
점점 더 많은 문자를 수용해야 할 필요성이 커짐에 따라 ASCII와 같은 단일 바이트 인코딩 체계는 지속 가능하지 않았다.

이로 인해 공간 요구 사항이 증가함에도 불구하고 훨씬 더 나은 용량을 가진 다중 바이트 인코딩 체계가 생겨났다.

BIG5 및 SHIFT-JIS는 더 넓은 문자 집합을 나타내기 위해 1바이트와 2바이트를 사용하기 시작한 다중 바이트 문자 인코딩 체계의 예이다. 이들 중 대부분은 문자 수가 훨씬 더 많은 중국어 및 유사한 스크립트를 나타내기 위해 만들어졌다.

convertToBinary 메소드를 한자 `語` 인코딩을 "Big5"로 한다.

```java
assertEquals(convertToBinary("語", "Big5"), "10111011 01111001");
```

위의 출력은 Big5 인코딩이 2바이트를 사용하여 `語` 문자를 나타냄을 보여준다.

별칭과 함께 문자 인코딩의 포괄적인 목록은 국제 번호 기관에서 관리한다.

## 4. 유니코드
인코딩도 중요하지만 디코딩도 표현을 이해하는데 중요하다는 것을 이해하는 것은 어렵지 않다. 이것은 일관되거나 호환되는 인코딩 체계가 널리 사용되는 경우에만 실제로 가능하다.

개별적으로 개발되고 지역에서 실행되는 다양한 인코딩 체계가 어려워지기 시작했다.

이 도전은 세계의 가능한 모든 문자를 수용할 수 있는 유니코드라는 단일 인코딩 표준을 탄생시켰다. 여기에는 사용중인 문자와 없어진 문자도 포함된다.

표준으로서 유니코드는 세계의 모든 가능한 문자에 대한 코드 포인트를 정의한다. 유니코드에서 문자 'T'의 코드 포인트는 십진수로 84이다. 우리는 일반적으로 이것을 유니코드에서 `U+0054`라고 하며 `U+` 뒤에 16진수가 붙을 뿐이다.

유니코드에서 코드 포인트는 1,114,112로 십진법으로 편리하게 소통할 수 있는 상당히 큰 숫자이기 때문에 16진수를 사용한다.

이러한 코드 포인트를 비트로 인코딩하는 방법은 유니코드 내의 특정 인코딩 체계에 따라 다르다.

### 1) UTF-32
UTF-32는 유니코드로 정의된 모든 코드 포인트를 나타내기 위해 4바이트를 사용하는 유니코드용 인코딩 체계이다. 분명히 모든 문자에 대해 4바이트를 사용하는 것은 공간 비효율적이다.

'T'와 같은 간단한 문자가 UTF-32에서 어떻게 표현되는지 본다. 앞서 소개한 convertToBinary 메소드를 사용한다.

```java
assertEquals(convertToBinary("T", "UTF-32"), "00000000 00000000 00000000 01010100");
```

위의 출력은 처음 3바이트가 그냥 낭비되는 공간인 'T' 문자를 나타내기 위해 4바이트를 사용하는 것을 보여준다.

### 2) UTF-8
UTF-8은 인코딩에 가변 길이의 바이트를 사용하는 유니코드의 또 다른 인코딩 체계이다. 일반적으로 문자를 인코딩하는데 단일 바이트를 사용하지만 필요한 경우 더 많은 수의 바이트를 사용할 수 있으므로 공간을 절약할 수 있다.

입력이 'T'이고 인코딩이 "UTF-8"인 convertToBinary 메서드를 다시 호출한다.

```java
assertEquals(convertToBinary("T", "UTF-8"), "01010100");
```

출력은 단일 바이트를 사용하는 ASCII와 정확히 유사하다. 사실, UTF-8은 ASCII와 완전히 역호환된다.

입력이 `語`이고 인코딩이 "UTF-8"인 convertToBinary 메서드를 다시 호출한다.

```java
assertEquals(convertToBinary("語", "UTF-8"), "11101000 10101010 10011110");
```

여기에서 볼 수 있듯이 UTF-8은 3바이트를 사용하여 `語` 문자를 나타낸다. 이를 가변 너비 인코딩이라고 한다.

UTF-8은 공간 효율성으로 인해 웹에서 가장 많이 사용되는 인코딩이다.

## 5. 자바 인코딩 지원
Java는 다양한 인코딩 및 상호 변환을 지원한다. Charset 클래스는 모든 Java 플랫폼 구현이 지원해야 하는 [표준 인코딩 세트](https://docs.oracle.com/en/java/javase/15/intl/supported-encodings.html#GUID-187BA718-195F-4C39-B0D5-F3FDF02C7205)를 정의한다.

여기에는 US-ASCII, ISO-8859-1, UTF-8 및 UTF-16이 포함된다. Java의 특정 구현은 선택적으로 추가 인코딩을 지원할 수 있다.

Java가 사용할 문자 집합을 선택하는 방법에는 몇 가지 미묘한 차이가 있다.

### 1) 기본 문자 집합
Java 플랫폼은 기본 charset이라는 속성에 크게 의존 한다. JVM(Java Virtual Machine)은 시작하는 동안 기본 문자 집합을 결정한다.

이는 JVM이 실행중인 기본 운영 체제의 로케일 및 문자 세트에 따라 다르다. 예를 들어 MacOS에서 기본 문자 집합은 UTF-8이다.

기본 문자 집합을 결정하는 방법이다.

```java
Charset.defaultCharset().displayName();
```

이 코드를 Windows 시스템에서 실행하면 다음과 같은 결과를 얻는다.

```text
windows-1252
```

이제 "windows-1252"는 영어로된 Windows 플랫폼의 기본 문자 집합이며, 이 경우 Windows에서 실행되는 JVM의 기본 문자 집합을 결정했다.

### 2) 기본 문자 집합을 사용하는 사용자
많은 Java API는 JVM에 의해 결정된 기본 문자 집합을 사용한다. 몇 가지 예를 들면 다음과 같다.

* InputStreamReader 및 FileReader

* OutputStreamWriter 및 FileWriter

* Formatter 및 Scanner

* URLEncoder 및 URLDecoder

따라서 이것은 문자 집합을 지정하지 않고 예제를 실행하는 경우 다음을 의미한다.

```java
new BufferedReader(new InputStreamReader(new ByteArrayInputStream(input.getBytes()))).readLine();
```

그런 다음 기본 charset을 사용하여 디코딩한다.

기본적으로 동일한 선택을 하는 여러 API가 있다.

따라서 기본 문자 집합은 안전하게 무시할 수 없는 중요성을 지닌다.

### 3) 기본 문자 집합의 문제
자바의 기본 charset은 JVM이 시작될 때 동적으로 결정된다는 것을 보았듯이, 이로 인해 플랫폼이 다른 운영 체제에서 사용될 때 플랫폼의 안정성이 떨어지거나 오류가 발생하기 쉽다.

예이다.

```java
new BufferedReader(new InputStreamReader(new ByteArrayInputStream(input.getBytes()))).readLine();
```

macOS에서는 UTF-8을 사용한다.

Windows에서 동일한 스니펫을 시도하면 Windows-1252를 사용하여 동일한 텍스트를 디코딩한다.

또는 macOS에서 파일을 작성한 다음 Windows에서 동일한 파일을 읽는다고 상상해 본다.

인코딩 방식이 다르기 때문에 데이터가 손실되거나 손상될 수 있다는 점을 이해하는 것은 어렵지 않다.

### 4) 기본 문자 집합 재정의
Java에서 기본 문자 집합을 결정하면 두 가지 시스템 속성이 생성된다.

* file.encoding : 이 시스템 속성의 값은 기본 문자 집합의 이름이다.

* sun.jnu.encoding : 이 시스템 속성의 값은 파일 경로를 encoding/decoding 할 때 사용되는 charset의 이름이다.

이제 명령줄 인수를 통해 이러한 시스템 속성을 재정의하는 것이 직관적이다.

```shell
-Dfile.encoding="UTF-8"
-Dsun.jnu.encoding="UTF-8"
```

그러나 이러한 속성은 Java에서 읽기 전용이라는 점에 유의해야 한다. 위와 같은 사용법은 설명서에 없다. 이러한 시스템 속성을 재정의하면 원하는 동작이나 예측 가능한 동작이 없을 수 있다.

따라서 Java에서 기본 charset을 무시하는 것을 피해야 한다.

### 5) Java가 이것을 해결하지 못하는 이유
[JEP(Java Enhancement Proposal)](https://openjdk.java.net/jeps/400)가 있는데, 이는 "UTF-8"을 기본 문자 집합으로 사용하는 대신 자바에서 사용하도록 규정한다.

이 JEP는 현재 초안 상태에 있으며, 이 JEP가 완료되면 앞에서 논의한 대부분의 문제를 해결할 것이다.

java.nio.file.Files에 있는 것과 같은 최신 API는 기본 문자 집합을 사용하지 않는다. 이러한 API의 메서드는 기본 문자 집합이 아닌 UTF-8로 문자 집합을 사용하여 문자 스트림을 읽거나 쓴다.

### 6) 이 문제 해결
일반적으로 기본 설정에 의존하는 대신 텍스트를 다룰 때 charset을 지정하도록 선택 해야 한다. 문자 대 바이트 변환을 처리하는 클래스에서 사용하려는 인코딩을 명시적으로 선언할 수 있다.

다행히도, 이미 charset을 지정하고 있다. 올바른 것을 선택하기만 하면 나머지는 Java가 처리한다.

이제 'ç'와 같은 악센트가 있는 문자는 인코딩 스키마 ASCII에 없으므로 이를 포함하는 인코딩이 필요하다는 것을 깨달아야 한다.

이제 동일한 입력으로 "UTF-8"로 인코딩하여 decodeText 메서드를 실행한다.

```text
The façade pattern is a software-design pattern.
```

기대했던 출력을 볼 수 있다.

여기에서 우리는 InputStreamReader의 생성자에서 우리의 필요에 가장 적합하다고 생각하는 인코딩을 설정했다. 이것은 일반적으로 Java에서 문자 및 바이트 변환을 처리하는 가장 안전한 방법이다.

마찬가지로 OutputStreamWriter 및 기타 많은 API는 생성자를 통해 인코딩 체계 설정을 지원한다.

### 7) 잘못된 입력 예외
바이트 시퀀스를 디코딩할 때 주어진 Charset에 대해 올바르지 않거나 합법적인 16비트 유니코드가 아닌 경우가 있다. 즉, 지정된 바이트 시퀀스에는 지정된 Charset에 매핑이 없다.

입력 시퀀스에 잘못된 입력이 있을 때 미리 정의된 세 가지 전략(또는 CodingErrorAction)이 있다.

* IGNORE는 잘못된 문자를 무시하고 코딩 작업을 재개한다.

* REPLACE는 출력 버퍼의 잘못된 문자를 대체하고 코딩 작업을 재개한다.

* REPORT는 MalformedInputException을 발생시킨다.

CharsetDecoder에 대한 기본 malformedInputAction은 REPORT이고 InputStreamReader에 있는 기본 디코더 의 기본 malformedInputAction은 REPLACE이다.

지정된 Charset, CodingErrorAction 유형 및 디코딩할 문자열을 수신하는 디코딩 함수를 정의한다.

```java
String decodeText(String input, Charset charset, 
  CodingErrorAction codingErrorAction) throws IOException {
    CharsetDecoder charsetDecoder = charset.newDecoder();
    charsetDecoder.onMalformedInput(codingErrorAction);
    return new BufferedReader(
      new InputStreamReader(
        new ByteArrayInputStream(input.getBytes()), charsetDecoder)).readLine();
}
```

따라서 US_ASCII로 "The façade pattern is a software design pattern."를 디코딩하면 각 전략의 출력은 달라진다. 먼저 잘못된 문자를 건너뛰는 CodingErrorAction.IGNORE를 사용한다.

```java
Assertions.assertEquals(
  "The faade pattern is a software design pattern.",
  CharacterEncodingExamples.decodeText(
    "The façade pattern is a software design pattern.",
    StandardCharsets.US_ASCII,
    CodingErrorAction.IGNORE));
```

두 번째 테스트에서는 잘못된 문자 대신 `�`을 넣는 CodingErrorAction.REPLACE를 사용한다.

```java
Assertions.assertEquals(
  "The fa��ade pattern is a software design pattern.",
  CharacterEncodingExamples.decodeText(
    "The façade pattern is a software design pattern.",
    StandardCharsets.US_ASCII,
    CodingErrorAction.REPLACE));
```

세 번째 테스트에서는 MalformedInputException을 발생 시키는 CodingErrorAction.REPORT를 사용한다.

```java
Assertions.assertThrows(
  MalformedInputException.class,
    () -> CharacterEncodingExamples.decodeText(
      "The façade pattern is a software design pattern.",
      StandardCharsets.US_ASCII,
      CodingErrorAction.REPORT));
```

## 6. 기타 인코딩이 중요한 곳
프로그래밍하는 동안 문자 인코딩만 고려할 필요는 없다. 텍스트는 다른 많은 곳에서 최종적으로 잘못될 수 있다.

이러한 경우 문제의 가장 일반적인 원인은 한 인코딩 체계에서 다른 인코딩 체계로 텍스트를 변환하는 것이므로 데이터 손실이 발생할 수 있다.

텍스트를 인코딩하거나 디코딩할 때 문제가 발생할 수 있는 몇 곳이다.

### 1) Text Editors
대부분의 경우 텍스트 편집기는 텍스트가 생성되는 곳이다다. vi, 메모장 및 MS Word를 포함하여 널리 사용되는 수많은 텍스트 편집기가 있다. 이러한 텍스트 편집기의 대부분을 사용하면 인코딩 체계를 선택할 수 있다. 그러므로 우리는 항상 그것들이 우리가 다루고 있는 텍스트에 적합한지 확인해야 한다.

### 2) File System
편집기에서 텍스트를 만든 후 일부 파일 시스템에 저장해야 한다. 파일 시스템은 실행 중인 운영 체제에 따라 다르다. 대부분의 운영 체제는 다중 인코딩 체계를 기본적으로 지원한다. 그러나 인코딩 변환으로 인해 데이터가 손실되는 경우가 여전히 있을 수 있다.

### 3) Network
FTP(File Transfer Protocol)와 같은 프로토콜을 사용하여 네트워크를 통해 전송되는 텍스트에는 문자 인코딩 간의 변환도 포함된다. 유니코드로 인코딩된 모든 항목의 경우 변환 손실 위험을 최소화하기 위해 바이너리로 전송하는 것이 가장 안전하다. 그러나 네트워크를 통해 텍스트를 전송하는 것은 데이터 손상의 덜 빈번한 원인 중 하나이다.

### 4) Databases
Oracle 및 MySQL과 같은 대부분의 인기 있는 데이터베이스는 데이터베이스 설치 또는 생성 시 문자 인코딩 체계 선택을 지원한다. 데이터베이스에 저장할 것으로 예상되는 텍스트에 따라 이것을 선택해야 한다. 이것은 인코딩 변환으로 인해 텍스트 데이터가 손상되는 더 자주 발생하는 장소 중 하나이다.

### 5) Browsers
대부분의 웹 응용 프로그램에서 텍스트를 만들고 브라우저와 같은 사용자 인터페이스에서 볼 의도로 텍스트를 다른 계층에 전달한다. 여기에서도 문자를 적절하게 표시할 수 있는 올바른 문자 인코딩을 선택하는 것이 필수적이다. Chrome, Edge와 같은 가장 널리 사용되는 브라우저에서는 설정을 통해 문자 인코딩을 선택할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-char-encoding](https://www.baeldung.com/java-char-encoding)
