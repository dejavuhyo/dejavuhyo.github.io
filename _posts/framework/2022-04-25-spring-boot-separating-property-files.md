---
title: Spring Boot properties 파일 개발 및 운영 분리
author: dejavuhyo
date: 2022-04-25 09:50:00 +0900
categories: [Application, Framework]
tags: [spring-boot-properties, properties-separate, properties-분리, properties-개발-운영]
---

## 1. properties 파일 분리
기존 application.properties 파일을 운영 및 개발로 분리하여 파일을 생성한다.

> application-{name}.properties

로컬환경은 local, 개발환경은 dev, 운영환경은 prd로 파일을 생성한다.

![properties](/assets/img/2022-04-25-spring-boot-separating-property-files/properties.png)

## 2. 애플리케이션 실행
Edit Configurations을 설정하는 방법과 properties 파일 설정하는 두 가지 방법이 있다.

### 1) Edit Configurations 설정
스프링 부트 실행 환경에서 Active profiles을 properties에 맞게 설정한다.

* Edit Configurations

![edit-configurations](/assets/img/2022-04-25-spring-boot-separating-property-files/edit-configurations.png)

* Configuration → Active profiles

![active-profiles](/assets/img/2022-04-25-spring-boot-separating-property-files/active-profiles.png)

### 2) properties 파일 설정
application.properties 파일에서 active 하려는 properties 파일명을 작성한다.

```properties
# application-dev.properties 설정
spring.profiles.active=dev
```

![active-properties](/assets/img/2022-04-25-spring-boot-separating-property-files/active-properties.png)

## 3. active properties 파일 실행 확인

![local](/assets/img/2022-04-25-spring-boot-separating-property-files/local.png)

![dev](/assets/img/2022-04-25-spring-boot-separating-property-files/dev.png)

## 4. 애플리케이션 실행오류
아무런 값을 지정하지 않거나, 다른 이름으로 설정하면 애플리케이션이 정상적으로 실행되지 않는다.

![application-failed-to-start](/assets/img/2022-04-25-spring-boot-separating-property-files/application-failed-to-start.png)

## [출처 및 참고]
* <https://zzang9ha.tistory.com/348>
* <https://royleej9.tistory.com/entry/Spring-properties-%EC%84%A4%EC%A0%95-%EB%B6%84%EB%A6%AC>
