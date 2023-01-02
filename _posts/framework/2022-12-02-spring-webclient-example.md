---
title: Spring WebClient 적용 예제
author: dejavuhyo
date: 2022-12-02 21:30:00 +0900
categories: [Application, Framework]
tags: [spring-webclient, webclient, rest-api, http-get, http-post, webclient-example, webclient-적용]
---

## 1. 적용

* pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

* Config

```java
import io.netty.channel.ChannelOption;
import io.netty.handler.timeout.ReadTimeoutHandler;
import io.netty.handler.timeout.WriteTimeoutHandler;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.client.reactive.ReactorClientHttpConnector;
import org.springframework.web.reactive.config.WebFluxConfigurer;
import org.springframework.web.reactive.function.client.WebClient;
import reactor.netty.http.client.HttpClient;

import java.util.concurrent.TimeUnit;

@Configuration
public class WebClientConfig implements WebFluxConfigurer {

    @Bean
    public WebClient webclient() {
        WebClient webClient = WebClient.builder()
                .clientConnector(new ReactorClientHttpConnector(getHttpClient()))
                .baseUrl("http://localhost:8081")
                .defaultCookie("cookieKey", "cookieValue")
                .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                .build();
        return webClient;
    }

    private HttpClient getHttpClient() {
        HttpClient httpClient = HttpClient.create()
                .option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 5000)
                .doOnConnected(conn -> conn.addHandlerLast(new ReadTimeoutHandler(5000, TimeUnit.MILLISECONDS))
                        .addHandlerLast(new WriteTimeoutHandler(5000, TimeUnit.MILLISECONDS)));
        return httpClient;
    }
}
```

* Controller

```java
import com.example.webclient.dto.WebDto;
import com.example.webclient.service.WebService;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;

import javax.annotation.Resource;

@RequiredArgsConstructor
@RestController
@RequestMapping(path = "/web")
public class WebController {

    @Resource
    private WebService webService;

    @PostMapping(path = "/mono")
    public WebDto getMono(@RequestBody WebDto webDto) {
        return webService.getMono(webDto);
    }

    @GetMapping(path = "/flux")
    public Flux<WebDto> getFlux() {
        return webService.getFlux();
    }

}
```

* DTO

```java
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import java.util.Date;

@Getter
@Setter
@NoArgsConstructor
public class WebDto {

    private int id;
    private String title;
    private String contents;
    private String writer;
    private Date regDate;
    private int viewCnt;

}
```

* Service

```java
import com.example.webclient.dto.WebDto;
import reactor.core.publisher.Flux;

public interface WebService {

    WebDto getMono(WebDto webDto);

    Flux<WebDto> getFlux();

}
```

* ServiceImpl

```java
import com.example.webclient.dto.WebDto;
import com.example.webclient.service.WebService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.reactive.function.client.WebClient;
import reactor.core.publisher.Flux;

@Service
public class WebServiceImpl implements WebService {

    @Autowired
    WebClient webClient;

    public WebDto getMono(WebDto webDto) {
        return webClient.post()
                .uri("/board")
                .bodyValue(webDto)
                .retrieve()
                .bodyToMono(WebDto.class)
                .block();
    }

    public Flux<WebDto> getFlux() {
        return webClient.get()
                .uri("/board")
                .retrieve()
                .bodyToFlux(WebDto.class);
    }

}
```

## [출처 및 참고]
* [https://github.com/dejavuhyo/web-client](https://github.com/dejavuhyo/web-client)
* [https://www.javadevjournal.com/spring/spring-webclient/](https://www.javadevjournal.com/spring/spring-webclient/)
* [https://www.codeusingjava.com/boot/webclient](https://www.codeusingjava.com/boot/webclient)
