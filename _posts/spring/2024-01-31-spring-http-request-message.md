---
title: Spring HTTP 요청 메시지 조회
author: dejavuhyo
date: 2024-01-31 09:53:00 +0900
categories: [Framework, Spring]
tags: [spring-request, httpservletrequest, httpservletresponse, requestbody]
---

## 1. JSON으로 보낸 HTTP 요청 메시지

### 1) HttpServletRequest, HttpServletResponse 이용
HttpServletRequest를 사용하여 HTTP 메시지 바디 데이터를 읽어 문자로 변환한다.

Stream은 바이트코드이기 때문에 인코딩 설정을 해야 한다.

```java
import jakarta.servlet.ServletInputStream;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.util.StreamUtils;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;

import java.io.IOException;
import java.nio.charset.StandardCharsets;

@Slf4j
@RestController
public class ApiController {
    // HttpServletRequest, HttpServletResponse
    @PostMapping("/servlet")
    public void servlet(HttpServletRequest request, HttpServletResponse response) throws IOException {
        ServletInputStream inputStream = request.getInputStream();
        String bodyMessage = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        log.info("bodyMessage={}", bodyMessage);

        response.setStatus(HttpServletResponse.SC_OK);
        response.setHeader("Content-Type", "application/json;charset=utf-8");
        response.setContentType("application/json");
        response.getWriter().write(bodyMessage);
    }
}
```

### 2) @RequestBody String 타입
`@RequestBody`는 요청 메시지 바디를 조회할 수 있는 어노테이션이다.

`@ReqiestHeader`는 헤더 정보를 조회할 수 있다.

```java
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
public class ApiController {
    // @RequestBody String
    @PostMapping("/requestBodyString")
    public String requestBody(@RequestBody String bodyMessage) throws JsonProcessingException {
        ObjectMapper objectMapper = new ObjectMapper(); // jackson
        DataDto dataDto = objectMapper.readValue(bodyMessage, DataDto.class);
        log.info("id={}, name={}", dataDto.getId(), dataDto.getName());

        return bodyMessage;
    }
}
```

### 3) @RequestBody 객체 타입
[HTTP 메시지 컨버터](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/converter/HttpMessageConverter.html)는 JSON을  객체로 변환해준다.

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
public class ApiController {
    // @RequestBody 객체
    @PostMapping("/requestBodyDto")
    public DataDto requestBodyJsonV3(@RequestBody DataDto dataDto) {
        log.info("id={}, name={}", dataDto.getId(), dataDto.getName());

        return dataDto;
    }
}
```

## [출처 및 참고]
* [https://jddng.tistory.com/220](https://jddng.tistory.com/220)
