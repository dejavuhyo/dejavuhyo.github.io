---
title: Spring Boot GetMapping, PostMapping
author: dejavuhyo
date: 2021-03-26 06:00:00 +0900
categories: [Application, Framework]
tags: [get, post, spring-boot-get, spring-boot-post, getmapping, postmapping, get-http, post-body]
---

## 1. GET

### 1) 특징

* URL에 변수(데이터)를 포함시켜 요청

* 데이터를 Header(헤더)에 포함하여 전송

* URL에 데이터가 노출되어 보안에 취약

* 캐싱할 수 있음

### 2) GetMapping 구현

* GetMappingController

```java
@RestController
public class GetMappingController {

    @GetMapping(value = "/getMappingTest")
    public String getMappingTest(@RequestParam String id, @RequestParam String name) {
        return "ID : " + id + ", NAME : " + name;
    }
}
```

* 테스트

![getmapping](/assets/img/2021-03-26-get-http-post-body-in-spring-boot/getmapping.png)

## 2. POST

### 1) 특징

* URL에 변수(데이터)를 노출하지 않고 요청

* 데이터를 Body(바디)에 포함

* URL에 데이터가 노출되지 않아 GET 방식보다 보안이 높음

* 캐싱할 수 없음

### 2) PostMapping 구현

* PostMappingController

```java
@RestController
public class PostMappingController {

    @PostMapping(value = "/postMappingTest")
    public String postMappingTest(@RequestBody DemoVO demoVO) {
        return "ID : " + demoVO.getId() + ", NAME : " + demoVO.getName();
    }
}
```

* DemoVO

```java
public class DemoVO {

    private String id;
    private String name;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

* 테스트

![postmapping](/assets/img/2021-03-26-get-http-post-body-in-spring-boot/postmapping.png)

## [출처 및 참고]
* [https://mangkyu.tistory.com/17](https://mangkyu.tistory.com/17)
