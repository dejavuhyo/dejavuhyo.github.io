---
title: Spring 4 AsyncRestTemplate
author: dejavuhyo
date: 2024-01-25 10:50:00 +0900
categories: [Framework, Spring]
tags: [spring-asyncresttemplate, asyncresttemplate, resttemplate, srping-rest]
---

## 1. AsyncRestTemplate
AsyncRestTemplate은 URL에 액세스하고 출력을 비동기적으로 반환한다. 출력은 결과를 가져오는 `get()` 메소드가 있는 ListenableFuture 형식이다. AsyncRestTemplate의 두 가지 메서드 `exchange()` 및 `Execution()`에 대한 예이다. Spring 4는 요청 객체를 준비하기 위해 AsyncRequestCallback 클래스를 제공한다.

## 2. org.springframework.web.client.AsyncRestTemplate
AsyncRestTemplate은 클라이언트 측 HTTP 액세스를 위한 기본 클래스이다. 액세스는 비동기식이다. ListenableFuture 객체를 반환하는 프로그램 내에서 URL을 비동기적으로 호출할 수 있다.

## 3. org.springframework.util.concurrent.ListenableFuture
ListenableFuture는 `java.util.concurrent.Future` 클래스에서 파생되었다. `ListenableFuture<T>`는 AsyncRestTemplate 메서드의 결과를 제공한다. 결과를 기다린다. `ListenableFuture.get()`을 사용하여 출력을 가져온다.

## 4. AsyncRestTemplate.exchange() 예제
`AsyncRestTemplate.exchange()` 메서드의 예제이다. HTTP 메소드를 사용하여 주어진 URI를 실행하고 `org.springframework.http.ResponseEntity`의 ListenableFuture로 응답을 반환한다.

* ExchangeMethodExample.java

```java
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.util.concurrent.ListenableFuture;
import org.springframework.web.client.AsyncRestTemplate;

import java.util.concurrent.ExecutionException;

public class ExchangeMethodExample {
    public static void main(String[] args) {
        AsyncRestTemplate asycTemp = new AsyncRestTemplate();
        String url = "http://google.com";
        HttpMethod method = HttpMethod.GET;
        Class<String> responseType = String.class;
        //create request entity using HttpHeaders
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.TEXT_PLAIN);
        HttpEntity<String> requestEntity = new HttpEntity<String>("params", headers);
        ListenableFuture<ResponseEntity<String>> future = asycTemp.exchange(url, method, requestEntity, responseType);
        try {
            //waits for the result
            ResponseEntity<String> entity = future.get();
            //prints body source code for the given URL
            System.out.println(entity.getBody());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

## 5. org.springframework.web.client.AsyncRequestCallback
AsyncRequestCallback은 `doWithRequest()` 메서드가 있는 인터페이스이다. 이 메소드는 AsyncRequestCallback 클래스를 사용하면서 정의해야 한다. 아래 예제는 사용이다.

## 6. AsyncRestTemplate.execute() 예제
`AsyncRestTemplate.execute()`는 HTTP 메서드를 비동기적으로 실행한다. 그리고 요청은 AsyncRequestCallback에 의해 구동되고 응답은 `org.springframework.web.client.ResponseExtractor`에 의해 구동된다.

* ExecuteMethodExample.java

```java
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.MediaType;
import org.springframework.http.client.AsyncClientHttpRequest;
import org.springframework.http.client.ClientHttpResponse;
import org.springframework.util.concurrent.ListenableFuture;
import org.springframework.web.client.AsyncRequestCallback;
import org.springframework.web.client.AsyncRestTemplate;
import org.springframework.web.client.ResponseExtractor;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ExecutionException;

public class ExecuteMethodExample {
    public static void main(String[] args) {
        AsyncRestTemplate asycTemp = new AsyncRestTemplate();
        String url = "http://google.com";
        HttpMethod method = HttpMethod.GET;
        //create request entity using HttpHeaders
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.TEXT_PLAIN);
        AsyncRequestCallback requestCallback = new AsyncRequestCallback() {
            @Override
            public void doWithRequest(AsyncClientHttpRequest arg0)
                    throws IOException {
                System.out.println(arg0.getURI());
            }
        };

        ResponseExtractor<String> responseExtractor = new ResponseExtractor<String>() {
            @Override
            public String extractData(ClientHttpResponse arg0)
                    throws IOException {
                return arg0.getStatusText();
            }
        };
        Map<String, String> urlVariable = new HashMap<>();
        urlVariable.put("q", "Concretepage");
        ListenableFuture<String> future = asycTemp.execute(url, method, requestCallback, responseExtractor, urlVariable);
        try {
            //waits for the result
            String result = future.get();
            System.out.println(result);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

## [출처 및 참고]
* [https://www.concretepage.com/spring-4/spring-4-asyncresttemplate-listenablefuture-example](https://www.concretepage.com/spring-4/spring-4-asyncresttemplate-listenablefuture-example)
