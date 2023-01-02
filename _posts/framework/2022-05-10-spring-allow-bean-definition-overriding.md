---
title: Spring Batch spring.main.allow-bean-definition-overriding 에러
author: dejavuhyo
date: 2022-05-10 16:00:00 +0900
categories: [Application, Framework]
tags: [spring-batch-error, batch-allow-bean-definition-overriding, allow-bean-definition-overriding, spring-allow-bean-definition-overriding]
---

## 1. 에러 메시지
Spring Batch 실행 중 createQueryProvider bean을 생성하는 중 에러가 발생했다.

![allow-bean-definition-overriding](/assets/img/2022-05-10-spring-allow-bean-definition-overriding/allow-bean-definition-overriding.png)

## 2. 에러 원인
다른 곳에서 이미 해당 bean을 생성해서 중복되는 현상이다.

## 3. 해결 방법
Spring Boot 2.1 부터는 overriding 옵션이 false로 default 설정이 된다.

Bean Overriding을 활성화하기 위해 `application.properties`에 `spring.main.allow-bean-definition-overriding=true` 옵션을 추가한다.

```properties
spring.main.allow-bean-definition-overriding=true
```

## [출처 및 참고]
* [https://n1tjrgns.tistory.com/172](https://n1tjrgns.tistory.com/172)
