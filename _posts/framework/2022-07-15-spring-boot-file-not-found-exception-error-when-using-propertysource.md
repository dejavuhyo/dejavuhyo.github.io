---
title: Spring Boot @PropertySource 사용시 FileNotFoundException 에러
author: dejavuhyo
date: 2022-07-15 12:20:00 +0900
categories: [Application, Framework]
tags: [spring-boot-file-not-found-exception, file-not-found-exception, spring-boot-propertysource-error, propertysource-error, propertysource-file-not-found-exception, file-not-found-exception-에러, spring-boot-propertysource-에러, 파일-찾기-에러]
---

## 1. 에러 내용
`secretKey.properties` 파일 위치는 `src/main/resources/properties/secretKey.properties` 이다.

```text
java.io.FileNotFoundException: Could not open ServletContext resource [/properties/secretKey.properties]
```

`@PropertySource` 설정 내용은 아래와 같다.

```java
@PropertySource("/properties/secretKey.properties")
```

## 2. 해결 방법
메이븐 빌드하면 resources 내 파일들이 classpath로 복사된다. 따라서 `classpath:` prefix를 붙여준다.

```java
@PropertySource("classpath:/properties/secretKey.properties")
```

## [출처 및 참고]
* <https://atoz-develop.tistory.com/entry/Spring-MVC-PropertySource-%EC%82%AC%EC%9A%A9-%EC%A4%91-FileNotFoundException-Could-not-open-ServletContext-resource-%EC%97%90%EB%9F%AC>
