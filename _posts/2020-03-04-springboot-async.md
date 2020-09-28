---
title: SpringBoot에서 비동기 처리 및 적용
author: Hyosik
date: 2020-03-04
categories: [Programming, Architecture]
tags: [springboot_async, async, 스프링부트_비동기, 비동기]
---

## 1. 개요
@Async 어노테이션을 bean에 넣으면 별도의 쓰레드에서 실행된다. 이를테면 호출자는 호출된 메소드가 완료될 때까지 기다릴 필요가 없다.

## 2. Async 기능 사용
자바 설정으로 비동기 처리를 사용하려면 간단히 설정 클래스에 @EnableAsync를 추가만 하면 된다.

## 3. 적용

* DemoApplication.java

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableAsync;

@EnableAsync
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

* AsyncConfig.java

```java
import org.springframework.scheduling.annotation.AsyncConfigurerSupport;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

public class AsyncConfig extends AsyncConfigurerSupport {

    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(2);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(500);
        executor.initialize();
        return executor;
    }
}
```

* DemoServiceImpl.java

```java
import com.example.demo.service.DemoService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class DemoServiceImpl implements DemoService {

    @Autowired
    RestTemplate restTemplate;

    /**
     * 비동기 rest api 호출
     */
    @Async
    @Override
    public void runAsync() {
        restTemplate.getForObject("http://localhost:8081/exampleApi", String.class);
    }
}
```

## 출처 및 참고
* <https://www.baeldung.com/spring-async>
* <https://springboot.tistory.com/38>
