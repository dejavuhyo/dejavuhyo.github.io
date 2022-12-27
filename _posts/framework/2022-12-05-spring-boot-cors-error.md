---
title: Spring Boot CORS 오류
author: dejavuhyo
date: 2022-12-05 21:40:00 +0900
categories: [Application, Framework]
tags: [cors, spring-boot-cors, spring-cors, cross-origin-resource-sharing, cors-error, cors-오류, 교차-출처-리소스-공유]
---

## 1. CORS란
교차 출처 리소스 공유(Cross-origin resource sharing, CORS), 교차 출처 자원 공유는 웹 페이지 상의 제한된 리소스를 최초 자원이 서비스된 도메인 밖의 다른 도메인으로부터 요청할 수 있게 허용하는 구조이다.

CORS는 교차 출처 요청을 허용하는 것이 안전한지 아닌지를 판별하기 위해 브라우저와 서버가 상호 통신하는 하나의 방법을 정의한다. 순수하게 동일한 출처 요청보다 더 많은 자유와 기능을 허용하지만, 단순히 모든 교차 출처 요청을 허용하는 것보다 더 안전하다.

CORS의 사양은 원래 W3C 권고안으로 배포되었으나 해당 문서는 쓸모없어진(obsolete) 상태이다. 현재 CORS를 재정의하면서 활발히 유지보수된 사양은 [WHATWG](https://ko.wikipedia.org/wiki/WHATWG)의 Fetch Living Standard이다.

## 2. CORS 동작 원리

cors-operation.png

## 3. 해결 방법

### 1) Configuration 설정
Global하게 적용하는 방법이다.

* WebConfig.java

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry corsRegistry) {
        corsRegistry.addMapping("/**") // CORS를 적용할 URL패턴 정의
                .allowedOrigins("*") // 자원 공유를 허락할 Origin 지정
                .allowedMethods("GET", "POST") // 허용할 HTTP method 지정
                .maxAge(3000); // 설정 시간만큼 pre-flight 리퀘스트 캐싱
    }
}
```

### 2) Annotation 설정
Controller 또는 메소드단에서 annotation을 통해 적용하는 방법이다. origins, methods, maxAge, allowedHeaders를 사용하면 된다.

* 클래스에 적용

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RequiredArgsConstructor
@RestController
@CrossOrigin(origins = "*", allowedHeaders = "*")
@RequestMapping(path = "/board")
public class Controller {

}
```

* 메소드에 적용

```java
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@Slf4j
@RequiredArgsConstructor
@RestController
@RequestMapping(path = "/board")
public class Controller {

    @CrossOrigin(origins = "*", allowedHeaders = "*")
    @GetMapping(path = "/{id}")
    public ResponseEntity<String> find(@PathVariable("id") String id) {
        ...
    }

}
```

## [출처 및 참고]
* <https://ko.wikipedia.org/wiki/%EA%B5%90%EC%B0%A8_%EC%B6%9C%EC%B2%98_%EB%A6%AC%EC%86%8C%EC%8A%A4_%EA%B3%B5%EC%9C%A0>
* <https://dev-pengun.tistory.com/entry/Spring-Boot-CORS-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0#1.%20CORS%EB%9E%80?>
