---
title: Java HttpClient ​​JSON 응답 Java 클래스 매핑
author: dejavuhyo
date: 2024-03-07 21:19:00 +0900
categories: [Language, Java]
tags: [java-json, json-response, java-httpclient, java-class, json-응답]
---

## 1. 예제 설정
Todo라는 간단한 프로그램을 작성한다. 프로그램은 가짜 REST API를 사용한다. GET 요청을 수행하고 나중에 응답을 조작한다.

### 1) 메이븐 종속성
Maven을 사용하여 종속성을 관리한다. 프로그램에서 라이브러리를 사용할 수 있도록 `pom.xml`에 [Gson](https://mvnrepository.com/artifact/com.google.code.gson) 및 [Jackson](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind) 종속성을 추가한다.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.16.0</version>
</dependency>
```

### 2) 샘플 프로젝트
빠른 프로토타이핑을 위해 가짜 REST API를 사용한다.

먼저 GET 요청이 호출될 때 샘플 API 응답이다.

```json
[
  {
    "userId": 1,
    "id": 1,
    "title": "delectus aut autem",
    "completed": false
  },
]
```

샘플 API는 4가지 속성이 포함된 JSON 응답을 반환한다. JSON 응답에는 두 개 이상의 개체가 있지만 단순성을 위해 이를 건너뛴다.

다음으로 데이터 바인딩을 위한 POJO 클래스를 만든다. 클래스 필드는 JSON 데이터 속성과 일치한다. `constructors`, `getter`, `setter`, `equals()` 및 `toString()`을 포함한다.

```java
public class Todo {

    int userId;
    int id;
    String title;
    boolean completed;

    // Standard constructors, getters, setters, equals(), and toString()
}
```

그런 다음 논리를 포함할 TodoAppClient 클래스를 만든다.

```java
public class TodoAppClient { 
    
    ObjectMapper objectMapper = new ObjectMapper();
    Gson gson = new GsonBuilder.create();
    
    // ...
}
```

또한 ObjectMapper 및 GsonBuilder의 새 인스턴스를 만들었다. 이렇게 하면 어떤 메서드에서든 액세스하고 재사용할 수 있다. 메소드 내에서 ObjectMapper 인스턴스를 생성하는 것은 비용이 많이 드는 작업일 수 있다.

마지막으로 샘플 API에서 GET 요청을 동기식으로 수행하는 메서드를 작성한다.

```java
public class TodoAppClient {

    // ...

    String sampleApiRequest() throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
          .uri(URI.create("https://jsonplaceholder.typicode.com/todos"))
          .build();

        HttpResponse<String> response = client.send(request, BodyHandlers.ofString());

        return response.body();
    }

    // ...
}
```

BodyHandlers의 `ofString()` 메서드는 응답 본문 바이트를 String으로 변환하는데 도움이 된다. 응답은 프로그램에서 쉽게 조작할 수 있는 JSON 문자열 이다. 이후 응답을 POJO 클래스에 매핑하는 방법을 확인한다.

`SampleApiRequest()` 메서드에 대한 단위 테스트를 작성한다.

```java
@Test
public void givenSampleRestApi_whenApiIsConsumedByHttpClient_thenCompareJsonString() throws Exception {
    TodoAppClient sampleApi = new TodoAppClient();
    assertNotNull(sampleApi.sampleApiRequest());
}
```

테스트에서는 API 호출의 응답이 null 이 아닌지 확인한다.

## 2. Jackson을 사용하여 POJO 클래스에 응답 매핑
Jackson은 널리 사용되는 Java JSON 라이브러리이다. 추가 조작을 위해 JSON을 직렬화 및 역직렬화하는데 도움이 된다. 이를 사용하여 예제 설정에서 JSON 응답을 역직렬화한다. 응답을 Todo POJO 클래스에 매핑한다.

```java
public Todo syncJackson() throws Exception {
    
    String response = sampleApiRequest();
    Todo[] todo = objectMapper.readValue(response, Todo[].class);
    
    return todo[0];
}
```

다음으로 Todo 배열을 선언했다. 마지막으로 ObjectMapper에서 `readValue()`를 호출하여 JSON 문자열을 POJO 클래스에 매핑했다.

반환된 Todo를 예상되는 Todo의 새 인스턴스와 비교하여 메서드를 테스트한다.

```java
@Test
public void givenSampleApiCall_whenResponseIsMappedByJackson_thenCompareMappedResponseByJackson() throws Exception {
    Todo expectedResult = new Todo(1, 1, "delectus aut autem", false); 
    TodoAppClient jacksonTest = new TodoAppClient();
    assertEquals(expectedResult, jacksonTest.syncJackson());
}
```

테스트에서는 예상 결과를 매핑된 JSON과 비교한다. 그것은 그들이 동등하다는 것을 확인한다.

## 3. Gson을 사용하여 POJO 클래스에 응답 매핑
Gson은 Google의 Java 라이브러리이다. Java 생태계에서는 Jackson 만큼 인기가 있다. 추가 처리를 위해 JSON 문자열을 Java 객체에 매핑하는데 도움이 된다. 이 라이브러리는 Java 객체를 JSON으로 변환할 수도 있다.

이를 사용하여 예제 설정의 JSON 응답을 해당 POJO 클래스인 Todo에 매핑한다. 로직이 포함된 클래스에 새로운 메소드인 `syncGson()`을 작성한다.

JSON 문자열을 역직렬화에 사용할 수 있도록 하기 위해 `SampleApi()`를 호출한다.

```java
public Todo syncGson() throws Exception {
    String response = sampleApiRequest();
    List<Todo> todo = gson.fromJson(response, new TypeToken<List<Todo>>(){}.getType());
    return todo.get(0);
}
```

먼저 Todo 유형의 목록을 만들었다. 그런 다음 `fromJson()` 메서드를 호출하여 JSON 문자열을 POJO 클래스에 매핑했다.

이제 추가 조작 및 처리를 위해 JSON 문자열이 POJO 클래스에 매핑된다.

예상 결과를 반환된 Todo와 비교하여 `syncGson()` 에 대한 단위 테스트를 작성한다.

```java
@Test
public void givenSampleApiCall_whenResponseIsMappedByGson_thenCompareMappedGsonJsonResponse() throws Exception {
    Todo expectedResult = new Todo(1, 1, "delectus aut autem", false);
    TodoAppClient gsonTest = new TodoAppClient();
    assertEquals(expectedResult, gsonTest.syncGson());
}
```

테스트에서는 예상 결과가 반환된 값과 일치한다.

## 4. 비동기식 호출
이제 API 호출을 비동기식으로 구현한다. 비동기식 패턴에서는 스레드가 서로 완료될 때까지 기다리지 않는다. 이 프로그래밍 패턴은 데이터 가져오기를 더욱 강력하고 확장 가능하게 만든다.

샘플 API를 비동기적으로 가져오고 JSON 응답을 POJO 클래스에 매핑한다.

### 1) Jackson을 사용하여 POJO 클래스에 대한 비동기 호출 및 매핑
두 개의 Java 라이브러리를 사용하여 JSON 응답을 역직렬화 한다. Jackson을 사용하여 비동기 호출 매핑을 구현한다. 먼저 TodoAppClient에 `readValueJackson()` 메서드를 생성한다.

이 메서드는 JSON 응답을 역직렬화하고 이를 POJO 클래스에 매핑한다.

```java
List<Todo> readValueJackson(String content) {
    try {
        return objectMapper.readValue(content, new TypeReference<List<Todo>>(){});
    } catch (IOException ioe) {
        throw new CompletionException(ioe);
    }
}
```

그런 다음 논리 클래스에 새 메서드를 추가한다.

```java
public Todo asyncJackson() throws Exception {
    HttpClient client = HttpClient.newHttpClient();
    HttpRequest request = HttpRequest.newBuilder()
      .uri(URI.create("https://jsonplaceholder.typicode.com/todos"))
      .build(); 
  
    TodoAppClient todoAppClient = new TodoAppClient();
    List<Todo> todo = HttpClient.newHttpClient()
      .sendAsync(request, BodyHandlers.ofString())
      .thenApply(HttpResponse::body)
      .thenApply(todoAppClient::readValueJackson)
      .get();
 
    return todo.get(0);
}
```

이 메서드는 비동기 GET 요청을 만들고 `readValueJackson()`을 호출하여 JSON 문자열을 POJO 클래스에 매핑한다.

마지막으로 역직렬화된 JSON 응답을 Todo의 예상 인스턴스와 비교하는 단위 테스트를 작성한다.

```java
@Test
public void givenSampleApiAsyncCall_whenResponseIsMappedByJackson_thenCompareMappedJacksonJsonResponse() throws Exception {
    Todo expectedResult = new Todo(1, 1, "delectus aut autem", false);
    TodoAppClient sampleAsyncJackson = new TodoAppClient();
    assertEquals(expectedResult, sampleAsyncJackson.asyncJackson());
}
```

예상 결과와 매핑된 JSON 응답이 동일하다.

### 2) Gson을 사용한 POJO 클래스에 대한 비동기 호출 및 매핑
비동기식 JSON 응답을 POJO 클래스에 매핑하여 프로그램을 더욱 향상해 본다. 먼저 TodoAppClient에서 JSON 문자열을 역직렬화하는 메서드를 만든다.

```java
List<Todo> readValueGson(String content) {
    return gson.fromJson(content, new TypeToken<List<Todo>>(){}.getType());
}
```

다음으로 프로그램 로직이 포함된 클래스에 새 메서드를 추가한다.

```java
public Todo asyncGson() throws Exception {
    HttpClient client = HttpClient.newHttpClient();
    HttpRequest request = HttpRequest.newBuilder()
      .uri(URI.create("https://jsonplaceholder.typicode.com/todos"))
      .build();
    TodoAppClient todoAppClient = new TodoAppClient();
    List<Todo> todo = HttpClient.newHttpClient()
      .sendAsync(request, BodyHandlers.ofString())
      .thenApply(HttpResponse::body)
      .thenApply(todoAppClient::readValueGson)
      .get();
  
    return todo.get(0);
}
```

이 메서드는 비동기 GET 요청을 만들고 JSON 응답을 POJO 클래스에 매핑한다. 마지막으로 `readValueGson()`을 호출하여 응답을 POJO 클래스에 매핑하는 프로세스를 수행한다.

단위 테스트를 작성한다. Todo의 예상되는 새 인스턴스를 매핑된 응답과 비교한다.

```java
@Test
public void givenSampleApiAsyncCall_whenResponseIsMappedByGson_thenCompareMappedGsonResponse() throws Exception {
    Todo expectedResult = new Todo(1, 1, "delectus aut autem", false); 
    TodoAppClient sampleAsyncGson = new TodoAppClient();
    assertEquals(expectedResult, sampleAsyncGson.asyncGson());
}
```

테스트에서는 예상 결과가 매핑된 JSON 응답과 일치한다.

## [출처 및 참고]
* [https://www.baeldung.com/java-httpclient-map-json-response](https://www.baeldung.com/java-httpclient-map-json-response)
