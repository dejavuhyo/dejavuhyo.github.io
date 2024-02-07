---
title: Spring Boot Properties
author: dejavuhyo
date: 2024-02-07 10:37:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-properties, spring-properties, properties, 스프링-부트-properties]
---

## 1. @PropertySource를 사용하여 Properties 파일 등록
`@PropertySource` 주석은 Spring 애플리케이션에 속성 파일을 등록하는데 사용된다.

### 1) Spring Boot 자동 application.properties 로드
기본적으로 Spring Boot는 시작될 때마다 자동으로 `application.properties`를 로드한다.` @Value` 주석을 사용하여 `application.properties`에 정의된 속성에 액세스할 수 있다.

다음 `application.properties` 파일이 있다고 가정한다.

* application.properties

```properties
application.name=Demo App
```

Spring `@Component`에서 이 속성에 액세스해야 하는 경우 `@Value` 주석을 사용할 수 있다.

```java
@Component
//This is optional as Spring boot loads application.properties by default
@PropertySource("classpath:application.properties")
public class AppProperties {

  @Value("${application.name}")
  private String appName;
}
```

`application.properties` 외에도 Spring Boot는 profile-specific 파일을 자동으로 로드한다. 예를 들어 활성 프로필이 다음인 경우 Spring Boot는 `application.properties` 파일과 함께 `application-dev.properties` 파일을 로드한다.

두 파일의 값 사이에 충돌이 있는 경우 `profile-specific`이 우선한다. 이상적으로는 `application.properties`에 기본값을 지정하고 이를 application-dev.properties 파일의 profile-specific 값으로 재정의해야 한다.

### 2) 사용자 정의 Properties 로드
Spring Boot가 기본적으로 읽는 파일을 변경하려면 이 `spring.config.name`속성을 사용할 수 있다.

```text
export SPRING_CONFIG_NAME=foo
```

Spring Boot 애플리케이션을 실행하면 `foo.properties` 파일에서 모든 속성이 로드된다.

다른 속성 파일이나 여러 속성 파일이 있는 경우 명시적으로 `@PropertySources` 주석을 사용하여 해당 속성 파일을 지정할 수 있다. 이 경우에도 `application.properties` 지정은 선택 사항이다.

```java
@Component
@PropertySources({
    @PropertySource("classpath:jms.properties"),
    @PropertySource("classpath:datasource.properties")
})
public class AppProperties {
	//...
}
```

### 3) 중복 속성 해결
동일한 이름을 가진 속성이 두 개 이상 있는 경우 속성 파일의 마지막 항목에서 속성 값이 선택된다.

중복된 속성 값은 예외를 발생시키지 않는다.

## 2. @Value를 사용하여 속성 값 주입
`@Value`는 속성 파일에서 채워진 기본값 표현식으로 필드를 초기화하기 위해 필드 또는 메서드/생성자 매개 변수 수준에서 사용된다.

* SpEL(Spring Expression Language) 표현식을 사용하면 `#{systemProperties.myProp}` 구문을 통해 값을 주입할 수 있다.

* 스타일 속성 자리 표시자 `${my.app.myProp}`를 사용하여 속성 값을 삽입할 수 있다.

또한 속성 key에 기본값을 할당할 수 있다. 이는 속성 키가 없거나 속성 파일에서 찾을 수 없는 경우 예외를 방지하는데 도움이 된다.

```java
@Component
@PropertySources({
    @PropertySource("classpath:jms.properties"),
    @PropertySource("classpath:datasource.properties")
})
public class AppProperties {

  @Value("${application.name:My App}")
  private String appName;

  @Value("${spring.datasource.url}")
  private String datasourceUrl;
}
```

문자열 배열이나 목록에 값 목록을 삽입 하려면 다음 예제를 사용할 수 있다. 참고로 속성 이름과 값은 다음과 같다.

* application.properties

```properties
app.environments=local,dev,test,prod
```

이러한 값을 문자열 배열에 주입하는 것은 기본적으로 지원된다.

```java
@Value("${app.environments}")
private String[] environments;
```

이러한 값을 List에 삽입하려면 SpEL 구문을 사용해야 한다.

```java
@Value("#{'${app.environments}'.split(',')}")
private List<String> environmentsList;
```

## 3. @ConfigurationProperties를 사용하여 필드를 속성 값에 바인딩
`@ConfigurationPropertiesBean`의 멤버 필드를 속성 파일에 정의된 속성 값과 바인딩하는데 사용된다. 바인딩은 주석이 달린 클래스에서 setter를 호출하거나 `@ConstructorBinding`이 사용 중인 경우 생성자 매개변수에 바인딩하여 수행된다.

@Value와 반대로 속성 값이 외부화되므로 SpEL 표현식은 평가되지 않는다.

예를 들어 `application.properties` 파일에 다음과 같은 속성이 있다고 가정한다.

* application.properties

```properties
spring.datasource.url=jdbc:h2:file:C:/temp/test
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.dialect=org.hibernate.dialect.H2Dialect
```

클래스 필드에서 이러한 속성을 바인딩하려면 속성 이름과 정확히 동일한 이름을 가진 필드를 만들어야 한다. 또한 prefix가 있는 경우 이를 언급해야 한다.

```java
@Data
@Component
@ConfigurationProperties(prefix = "spring.datasource")
public class DatasourceProps {

  private String url;
  private String username;
  private String password;
  private String driverClassName;
  private String dialect;
}
```

위 속성이 별도 파일 `datasource.properties`의 일부인 경우 `@PropertySource` 속성 파일 이름을 지정하는데 사용할 수 있다.

* datasource.properties

```properties
@Data
@Component
@PropertySource("classpath:datasource.properties")
@ConfigurationProperties(prefix = "spring.datasource")
public class DatasourceProps {

  //fields
}
```

## 4. 속성 값 확인
spring-boot-starter-validation 프로젝트에서 모듈을 가져오는 것부터 시작한다. 이 모듈은 `JSR-303` 사양을 구현하는 `hibernate-validator` 프로젝트를 가져온다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

필드 속성 바인딩을 검증하기 위해 `@Validated` 주석을 사용할 수 있다. `@Valid` 검증 그룹의 사양을 지원하는 `JSR-303`의 변형이다 .

`@Validated` 외에도 `javax.validation.constraints` 주석을 사용하여 필드에 특정 제약 조건을 적용해야 한다. 이러한 유효성 검사 중 하나라도 실패하면 IllegalStateException으로 인해 애플리케이션이 시작되지 않는다.

```java
@Data
@Component
@Validated
public class AppProperties {

  @NotEmpty
  @Value("${application.name}")
  private String appName;

  @NotEmpty
  @Value("${spring.datasource.url}")
  private String datasourceUrl;
}
```

## 5. 추가 구성 파일 포함
추가 속성 파일을 포함하려면 `application.properties` 또는 `application.yml` 파일 내에 `spring.config.import` 속성을 사용하면 된다. Import는 검색된 대로 처리되며, Import를 선언하는 문서 바로 아래에 삽입된 추가 문서로 처리된다.

예를 들어, a`pplication.properties` 파일에 다음 import 문이 있을 수 있다.

```properties
application.name=Demo App
spring.config.import=optional:file:./dev.properties
```

위의 가져오기는 현재 작업 디렉토리에서 `dev.properties` 파일을 검색하고 가져오려고 시도한다. 파일이 발견되면 해당 값이 가져오기를 트리거한 파일보다 우선한다. 파일을 찾을 수 없으면 오류가 보고되지 않는다.

기존 속성 파일에서 `spring.config.import` 문의 위치는 중요하지 않다. 위에서 설명한 것과 같이 항상 동일한 결과가 나올 것이다.

여러 위치를 지정하면 모든 위치가 정의된 순서대로 처리되며 이후 가져오기가 우선 적용된다. 여러 속성 파일이 포함된 디렉터리를 지정할 수도 있다.

```properties
spring.config.import=classpath:datasource.properties,
										  classpath:mysql-properties.yml,
										  optional:file:./cloud-deployment.properties,
										  classpath:test-properties/
```

디렉토리를 가져오면 로드된 파일이 알파벳순으로 정렬된다. 다른 주문이 필요한 경우 각 위치를 별도의 가져오기로 나열해야 한다.

`spring.config.import` 속성은 서버 시작 인수를 사용하여 설정할 수도 있다.

```shell
$ java -jar myproject.jar --spring.config.import=\
    classpath:datasource.properties,\
    classpath:mysql-properties.properties,\
    optional:file:./cloud-deployment.properties,\
    classpath:test-properties/
```

## [출처 및 참고]
* [https://howtodoinjava.com/spring-boot/properties-with-spring-boot/](https://howtodoinjava.com/spring-boot/properties-with-spring-boot/)
