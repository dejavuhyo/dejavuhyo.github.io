---
title: Spring Boot Annotations
author: dejavuhyo
date: 2024-04-02 11:16:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-annotations, spring-annotations, spring-boot, 스프링-부트-어노테이션, 스프링-어노테이션]
---

## 1. @SpringBootApplication
이 주석을 사용하여 Spring Boot 애플리케이션의 기본 클래스를 표시한다.

```java
@SpringBootApplication
class VehicleFactoryApplication {

    public static void main(String[] args) {
        SpringApplication.run(VehicleFactoryApplication.class, args);
    }
}
```

`@SpringBootApplication`은 `@Configuration`, `@EnableAutoConfiguration` 및 `@ComponentScan` 주석을 기본 속성으로 캡슐화한다.

## 2. @EnableAutoConfiguration
`@EnableAutoConfiguration`은 이름에서 알 수 있듯이 자동 구성을 활성화한다. 이는 Spring Boot가 클래스 경로에서 자동 구성 Bean을 찾아 자동으로 적용한다는 의미이다.

`@Configuration`과 함께 이 주석을 사용해야 한다.

```java
@Configuration
@EnableAutoConfiguration
class VehicleFactoryConfig {}
```

## 3. Auto-Configuration 조건
일반적으로 사용자 정의 자동 구성을 작성할 때 Spring이 이를 조건부로 사용하기를 원한다.

`@Configuration` 클래스 또는 `@Bean` 메소드에 주석을 배치할 수 있다.

### 1) @ConditionalOnClass 및 @ConditionalOnMissingClass
이러한 조건을 사용하면 Spring은 주석 인수의 클래스가 present/absent하는 경우에만 표시된 자동 구성 Bean을 사용한다.

```java
@Configuration
@ConditionalOnClass(DataSource.class)
class MySQLAutoconfiguration {
    //...
}
```

### 2) @ConditionalOnBean 및 @ConditionalOnMissingBean
특정 빈의 유무에 따라 조건을 정의하려는 경우 이러한 주석을 사용할 수 있다.

```java
@Bean
@ConditionalOnBean(name = "dataSource")
LocalContainerEntityManagerFactoryBean entityManagerFactory() {
    // ...
}
```

### 3) @ConditionalOnProperty
이 주석을 사용하면 속성 값에 대한 조건을 만들 수 있다.

```java
@Bean
@ConditionalOnProperty(
    name = "usemysql", 
    havingValue = "local"
)
DataSource dataSource() {
    // ...
}
```

### 4) @ConditionalOnResource
특정 리소스가 존재할 때만 Spring이 정의를 사용하도록 만들 수 있다.

```java
@ConditionalOnResource(resources = "classpath:mysql.properties")
Properties additionalProperties() {
    // ...
}
```

### 5) @ConditionalOnWebApplication 및 @ConditionalOnNotWebApplication
이러한 주석을 사용하면 현재 애플리케이션이 웹 애플리케이션인지 아닌지에 따라 조건을 만들 수 있다.

```java
@ConditionalOnWebApplication
HealthCheckController healthCheckController() {
    // ...
}
```

### 6) @ConditionalExpression
Spring은 SpEL 표현식이 true로 평가될 때 표시된 정의를 사용한다.

```java
@Bean
@ConditionalOnExpression("${usemysql} && ${mysqlserver == 'local'}")
DataSource dataSource() {
    // ...
}
```

### 7) @Conditional
훨씬 더 복잡한 조건의 경우 사용자 정의 조건을 평가하는 클래스를 만들 수 있다. `@Conditional`과 함께 이 사용자 정의 조건을 사용하도록 Spring에 지시한다.

```java
@Conditional(HibernateCondition.class)
Properties additionalProperties() {
    //...
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-annotations](https://www.baeldung.com/spring-boot-annotations)
