---
title: Spring Boot properties를 yml로 변환
author: dejavuhyo
date: 2024-04-12 13:28:00 +0900
categories: [Framework, Spring]
tags: [spring-properties, spring-yml, properties-yml, properties-convert, yml-convert]
---

## 1. Properties와 YML의 차이점
`application.properties` 파일에서 속성은 한 줄 구성으로 저장된다. Spring Boot는 속성 파일을 기본 파일로 생성한다.

```properties
spring.datasource.url=jdbc:h2:mem:testDB
spring.datasource.username=user
spring.datasource.password=testpwd
```

반면에 `application.yml`을 만들 수 있다. 이는 속성 파일과 비교하여 계층적 데이터가 있을 때 읽기 쉬운 YML 기반 파일이다.

```yml
spring:
  datasource:
    url: 'jdbc:h2:mem:testDB'
    username: user
    password: testpwd
```

YML 기반 구성 덕분에 반복되는 접두사(spring.datasource)를 추가할 필요가 없다.

## 2. Properties를 YML로 변환

### 1) IntelliJ Plugin
IntelliJ를 IDE로 사용하는 경우 플러그인을 설치하여 변환을 수행할 수 있다.

* File → Settings → Plugins → Install

"Convert YAML and Properties file" 검색

![plugin-properties-to-yml](/assets/img/2024-04-12-spring-boot-convert-properties-to-yml/plugin-properties-to-yml.png)

`application.properties` 파일에서 마우스 오른쪽 클릭하고 "Convert YAML and Properties file"를 선택한다.

### 2) Online Website
properties 파일의 콘텐츠를 직접 복사하여 [simplestep](https://env.simplestep.ca/) 변환기 웹사이트에 붙여넣을 수도 있다.

보안을 위해 웹사이트에서 변환시 비밀번호를 입력하지 않도록 해야 한다.

![online-tool-simplestep](/assets/img/2024-04-12-spring-boot-convert-properties-to-yml/online-tool-simplestep.png)

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-convert-application-properties-to-application-yml](https://www.baeldung.com/spring-boot-convert-application-properties-to-application-yml)
