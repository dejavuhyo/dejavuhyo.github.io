---
title: Spring Enum을 요청 매개변수로 사용
author: dejavuhyo
date: 2023-09-19 08:35:00 +0900
categories: [Framework, Spring]
tags: [spring-enum, spring-param, enum-request-param, enum-param, 스프링-매개변수]
---

## 1. Enums을 요청 매개변수로 사용
예제에 대한 Enums을 정의한다.

```java
public enum Modes {
    ALPHA, BETA;
}
```

그런 다음 이 열거형을 Spring 컨트롤러의 RequestParameter로 사용할 수 있다.

```java
@GetMapping("/mode2str")
public String getStringToMode(@RequestParam("mode") Modes mode) {
    // ...
}
```

또는 PathVariable로 사용할 수도 있다.

```java
@GetMapping("/findbymode/{mode}")
public String findByEnum(@PathVariable("mode") Modes mode) {
    // ...
}
```

`/mode2str?mode=ALPHA`와 같은 웹 요청을 할 때 요청 매개변수는 String 객체이다. Spring은 StringToEnumConverterFactory 클래스를 사용하여 이 String 객체를 Enum 객체로 변환하려고 시도할 수 있다.

백엔드 변환에서는 `Enum.valueOf` 메서드를 사용한다. 따라서 입력 이름 문자열은 선언된 열거형 값 중 하나와 정확히 일치해야 한다.

`/mode2str?mode=unknown`과 같이 열거형 값 중 하나와 일치하지 않는 문자열 값으로 웹 요청을 하면 Spring은 이를 지정된 열거형 유형으로 변환하지 못한다. 이 경우 ConversionFailedException이 발생한다.

## 2. 맞춤형 변환기
Java에서는 상수이므로 열거형 값을 대문자로 정의하는 것이 좋은 습관으로 간주된다. 그러나 요청 URL에 소문자를 지원하고 싶을 수도 있다.

이 경우 사용자 지정 변환기를 만들어야 한다.

```java
public class StringToEnumConverter implements Converter<String, Modes> {
    @Override
    public Modes convert(String source) {
        return Modes.valueOf(source.toUpperCase());
    }
}
```

사용자 정의 변환기를 사용하려면 이를 Spring 구성에 등록 해야 한다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverter(new StringToEnumConverter());
    }
}
```

## 3. 예외 처리
StringToEnumConverter의 `Enum.valueOf` 메소드는 모드 열거형에 일치하는 상수가 없는 경우 IllegalArgumentException을 발생시킨다. 요구 사항에 따라 사용자 지정 변환기에서 이 예외를 다양한 방식으로 처리할 수 있다.

예를 들어, 일치하지 않는 String에 대해 변환기가 null을 반환하도록 간단히 설정할 수 있다.

```java
public class StringToEnumConverter implements Converter<String, Modes> {
    @Override
    public Modes convert(String source) {
        try {
            return Modes.valueOf(source.toUpperCase());
        } catch (IllegalArgumentException e) {
            return null;
        }
    }
}
```

그러나 사용자 정의 변환기에서 로컬로 예외를 처리하지 않으면 Spring은 호출하는 컨트롤러 메서드에 ConversionFailedException 예외를 발생시킨다. 이 예외를 처리하는 방법에는 여러 가지가 있습니다.

예를 들어 전역 예외 처리기 클래스를 사용할 수 있다.

```java
@ControllerAdvice
public class GlobalControllerExceptionHandler {
    @ExceptionHandler(ConversionFailedException.class)
    public ResponseEntity<String> handleConflict(RuntimeException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.BAD_REQUEST);
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-enum-request-param](https://www.baeldung.com/spring-enum-request-param)
