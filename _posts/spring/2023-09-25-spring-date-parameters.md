---
title: Spring MVC 세션 속성
author: dejavuhyo
date: 2023-09-25 07:45:00 +0900
categories: [Framework, Spring]
tags: [spring-date, date-parameters, 스프링-날짜, 날짜-매개변수]
---

## 1. 문제
Date, LocalDate 및 LocalDateTime 매개 변수를 허용하는 세 가지 메서드가 있는 컨트롤러를 확인한다.

```java
@RestController
public class DateTimeController {

    @PostMapping("/date")
    public void date(@RequestParam("date") Date date) {
        // ...
    }

    @PostMapping("/localdate")
    public void localDate(@RequestParam("localDate") LocalDate localDate) {
        // ...
    }

    @PostMapping("/localdatetime")
    public void dateTime(@RequestParam("localDateTime") LocalDateTime localDateTime) {
        // ...
    }
}
```

ISO 8601에 따라 형식이 지정된 매개변수를 사용하여 해당 메서드에 POST 요청을 보내면 예외가 발생한다.

예를 들어, "2018-10-22"를 /date 엔드포인트로 보내면 다음과 유사한 메시지와 함께 잘못된 요청 오류가 발생한다.

```text
Failed to convert value of type 'java.lang.String' to required type 'java.time.LocalDate'; 
  nested exception is org.springframework.core.convert.ConversionFailedException.
```

이는 기본적으로 Spring이 문자열 매개변수를 날짜 또는 시간 객체로 변환할 수 없기 때문이다.

## 2. 요청 수준에서 날짜 매개변수 변환
이 문제를 처리하는 방법 중 하나는 매개변수에 `@DateTimeFormat` 주석을 추가하고 형식 지정 패턴 매개변수를 제공하는 것이다.

```java
@RestController
public class DateTimeController {

    @PostMapping("/date")
    public void date(@RequestParam("date") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) Date date) {
        // ...
    }

    @PostMapping("/local-date")
    public void localDate(@RequestParam("localDate") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate localDate) {
        // ...
    }

    @PostMapping("/local-date-time")
    public void dateTime(@RequestParam("localDateTime") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE_TIME) LocalDateTime localDateTime) {
        // ...
    }
}
```

이렇게 하면 문자열이 ISO 8601 형식을 사용하여 형식화되는 경우 문자열이 날짜 개체로 올바르게 변환된다.

`@DateTimeFormat` 주석에 패턴 매개변수를 제공하여 자체 변환 패턴을 사용할 수도 있다.

```java
@PostMapping("/date")
public void date(@RequestParam("date") @DateTimeFormat(pattern = "dd.MM.yyyy") Date date) {
    // ...
}
```

## 3. 애플리케이션 수준에서 날짜 매개변수 변환
Spring에서 날짜 및 시간 객체 변환을 처리하는 또 다른 방법은 전역 구성을 제공하는 것이다. 공식 문서에 따라 WebMvcConfigurationSupport 구성 및 해당 mvcConversionService 메서드를 확장해야 한다.

```java
@Configuration
public class DateTimeConfig extends WebMvcConfigurationSupport {

    @Bean
    @Override
    public FormattingConversionService mvcConversionService() {
        DefaultFormattingConversionService conversionService = new DefaultFormattingConversionService(false);

        DateTimeFormatterRegistrar dateTimeRegistrar = new DateTimeFormatterRegistrar();
        dateTimeRegistrar.setDateFormatter(DateTimeFormatter.ofPattern("dd.MM.yyyy"));
        dateTimeRegistrar.setDateTimeFormatter(DateTimeFormatter.ofPattern("dd.MM.yyyy HH:mm:ss"));
        dateTimeRegistrar.registerFormatters(conversionService);

        DateFormatterRegistrar dateRegistrar = new DateFormatterRegistrar();
        dateRegistrar.setFormatter(new DateFormatter("dd.MM.yyyy"));
        dateRegistrar.registerFormatters(conversionService);

        return conversionService;
    }
}
```

먼저 false 매개변수를 사용하여 DefaultFormattingConversionService를 생성한다. 이는 Spring이 기본적으로 어떤 포맷터도 등록하지 않음을 의미한다.

그런 다음 날짜 및 날짜-시간 매개변수에 대한 사용자 정의 형식을 등록해야 한다. 두 개의 사용자 정의 형식 등록 기관을 등록하여 이를 수행한다. 첫 번째 항목인 DateTimeFormatterRegistar는 LocalDate 및 LocaDateTime 개체의 구문 분석을 담당한다. 두 번째 항목인 DateFormattingRegistrar는 Date 개체를 처리한다.

## 4. 속성 파일에서 날짜-시간 구성
Spring은 또한 애플리케이션 속성 파일을 통해 전역 날짜-시간 형식을 설정하는 옵션을 제공한다. 날짜, 날짜-시간 및 시간 형식에 대한 세 가지 개별 매개변수가 있다.

```property
spring.mvc.format.date=yyyy-MM-dd
spring.mvc.format.date-time=yyyy-MM-dd HH:mm:ss
spring.mvc.format.time=HH:mm:ss
```

이러한 매개변수는 모두 iso 값으로 대체될 수 있다. 예를 들어 날짜-시간 매개변수를 다음과 같이 설정한다.

```property
spring.mvc.format.date-time=iso
```

ISO-8601 형식과 동일하다.

```property
spring.mvc.format.date-time=yyyy-MM-dd HH:mm:ss
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-date-parameters](https://www.baeldung.com/spring-date-parameters)
