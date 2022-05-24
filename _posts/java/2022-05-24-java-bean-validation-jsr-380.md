---
title: Java Bean 유효성 검사 (JSR 380)
author: dejavuhyo
date: 2022-05-24 16:50:00 +0900
categories: [Language, Java]
tags: [java-bean-validation, bean-validation, jsr-380, hibernate-validator, java-bean-유효성-검사, bean-유효성-검사]
---

## 1. JSR 380
JSR 380은 Jakarta EE 및 JavaSE의 일부인 빈 유효성 검사를 위한 Java API 사양이다. 이것은 `@NotNull`, `@Min` 및 `@Max`와 같은 주석을 사용하여 빈의 속성이 특정 기준을 충족하는지 확인한다.

이 버전에는 Java 8 이상이 필요하며 유형 주석 및 Optional 및 LocalDate와 같은 새로운 유형에 대한 지원과 같은 Java 8에 추가된 새로운 기능을 활용한다.

사양에 대한 전체 정보는 [JSR 380](https://jcp.org/en/jsr/detail?id=380)을 참고한다.

## 2. JSR 380 특징

* Bean validation은 애플리케이션 개발시 자주 반복되는 유효성 검사 패턴을 정리한 Java EE 표준이다.

* 1.0 (JSR303), 1.1 (JSR349)를 거쳐 2.0 (JSR380)이 현재 최신 버전이다. 최소 Java 8이 요구되며 하위 버전은 지원하지 않는다.

* JSR 380의 인터페이스는 javax.validation 그룹의 validation-api 아티팩트에서 확인할 수 있고, 인터페이스에 대한 대표적인 구현체는 hibernate-validator가 존재한다. Spring Boot 2.0 기반의 프로젝트라면 이미 적용되어 있기에 별도의 Dependency 관리는 필요 없다.

* Spring boot에서는 Controller Method의 Parameter 레벨에 JSR380이 제공하는 `@Valid` Annotation을 이용한다.

## 3. Dependencies

### 1) Validation API
JSR 380 사양에 따라 validation-api 종속성은 표준 유효성 검사 API를 포함한다.

```xml
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>2.0.1.Final</version>
</dependency>
```

### 2) Validation API 참조 구현
Hibernate Validator는 검증 API의 참조 구현이다. 이를 사용하려면 다음 종속성을 추가해야 한다.

```xml
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.0.13.Final</version>
</dependency>
```

> hibernate-validator는 Hibernate의 지속성 측면과 완전히 별개이다. 따라서 종속성으로 추가함으로써 이러한 지속성 측면을 프로젝트에 추가하지 않는다.

### 3) 표현 언어 종속성
JSR 380은 변수 보간을 지원하여 위반 메시지 내부의 표현식을 허용한다. 이러한 표현식을 구문 분석하기 위해 표현식 언어 사양의 구현을 포함하는 GlassFish의 javax.el 종속성을 추가합니다.

```xml
<dependency>
    <groupId>org.glassfish</groupId>
    <artifactId>javax.el</artifactId>
    <version>3.0.0</version>
</dependency>
```

## 4. Validation Annotations 사용

```java
import javax.validation.constraints.AssertTrue;
import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;
import javax.validation.constraints.Email;

public class User {

    @NotNull(message = "Name cannot be null")
    private String name;

    @AssertTrue
    private boolean working;

    @Size(min = 10, max = 200, message = "About Me must be between 10 and 200 characters")
    private String aboutMe;

    @Min(value = 18, message = "Age should not be less than 18")
    @Max(value = 150, message = "Age should not be greater than 150")
    private int age;

    @Email(message = "Email should be valid")
    private String email;

    // standard setters and getters
}
```

예제에 사용된 모든 주석은 표준 JSR 주석이다.

* __`@NotNull`__: 주석이 달린 속성 값이 null이 아닌지 확인한다.

* __`@AssertTrue`__ : 주석이 달린 속성 값이 true인지 확인한다.

* __`@Size`__: 주석이 달린 속성 값이 속성 min과 max 사이의 크기인지 확인한다. String, Collection, Map, array 속성에 적용할 수 있다.

* __`@Min`__: 주석이 달린 속성에 값 속성 이상의 값이 있는지 확인한다.

* __`@Max`__: 주석이 달린 속성의 값이 값 속성보다 크지 않은지 확인한다.

* __`@Email`__: 주석이 달린 속성이 유효한 이메일 주소인지 확인한다.

일부 주석은 추가 속성을 허용하지만 메시지 속성은 모든 속성에 공통이다. 이는 해당 속성의 값이 유효성 검사에 실패할 때 일반적으로 렌더링되는 메시지이다.

JSR에서 찾을 수 있는 몇 가지 추가 주석이다.

* __`@NotEmpty`__: 속성이 null 또는 비어 있지 않은지 확인한다. String, Collection, Map 또는 Array 값에 적용할 수 있다.

* __`@NotBlan`k__: 텍스트 값에만 적용할 수 있으며 속성이 null 또는 공백이 아닌지 확인한다.

* __`@Positive`, `@PositiveOrZero`__: 숫자 값에 적용되고 완전히 양수인지 또는 0을 포함한 양수인지 검증한다.

* __`@Negative`, `@NegativeOrZero`__: 숫자 값에 적용되고 완전히 음수인지 또는 0을 포함한 음수인지 검증한다.

* __`@Past`, `@PastOrPresent`__: 날짜 값이 현재를 포함하여 과거 또는 과거인지 확인한다. Java 8에 추가된 날짜 유형을 포함하여 날짜 유형에 적용할 수 있다.

* __`@Future`, `@FutureOrPresent`__: 날짜 값이 미래 또는 현재를 포함하여 미래인지 확인한다.

## [출처 및 참고]
* <https://jcp.org/en/jsr/detail?id=380>
* <https://www.baeldung.com/javax-validation>
