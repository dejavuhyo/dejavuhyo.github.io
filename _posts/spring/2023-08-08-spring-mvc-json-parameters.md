---
title: Spring MVC JSON Parameters
author: dejavuhyo
date: 2023-08-08 07:20:00 +0900
categories: [Framework, Spring]
tags: [spring-json-parameters, spring-json, json-parameters]
---

## 1. Spring MVC의 JSON 매개변수
JSON을 사용하여 데이터를 보내거나 받는 것은 웹 개발자 사이에서 일반적인 관행이다. JSON 문자열의 계층 구조는 HTTP 요청 매개변수를 표현하기 위해 보다 간결하고 사람이 읽을 수 있는 방법을 제공한다.

기본적으로 Spring MVC는 String과 같은 간단한 데이터 유형에 대한 즉시 사용 가능한 데이터 바인딩을 제공한다. 이를 위해 기본 제공 [속성 편집기 목록](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/propertyeditors/package-summary.html)을 사용한다.

그러나 실제 프로젝트에서는 더 복잡한 데이터 유형을 바인딩해야 할 수 있다. 예를 들어 JSON 매개변수를 모델 객체에 매핑할 수 있으면 편리할 수 있다.

## 2. POST에서 JSON 데이터 보내기
Spring은 POST 요청을 통해 JSON 데이터를 보내는 간단한 방법을 제공한다. 기본 제공 `@RequestBody` 주석은 요청 본문에 캡슐화된 JSON 데이터를 특정 모델 개체로 자동 역직렬화할 수 있다.

일반적으로 요청 본문을 직접 구문 분석할 필요는 없다. Jackson 라이브러리를 사용하여 모든 작업을 수행 할 수 있다.

Spring MVC에서 POST 요청을 통해 JSON 데이터를 보내는 방법이다.

먼저 전달된 JSON 데이터를 나타내는 모델 개체를 만들어야 한다. 예를 들어 Product 클래스이다.

```java
public class Product {

    private int id;
    private String name;
    private double price;

    // default constructor + getters + setters
}
```

두 번째로 POST 요청을 수락하는 Spring 핸들러 메서드를 정의한다.

```java
@PostMapping("/create")
@ResponseBody
public Product createProduct(@RequestBody Product product) {
    // custom logic
    return product;
}
```

`@RequestBody`로 제품 인수에 주석을 달면 클라이언트에서 보낸 JSON 데이터를 바인딩하기에 충분하다.

[cURL](https://www.baeldung.com/curl-rest)을 사용하여 POST 요청을 테스트할 수 있다.

## 3. GET에서 JSON 파라미터 보내기
Spring MVC는 `@RequestParam`을 제공하여 GET 요청에서 쿼리 매개변수를 추출한다. 그러나 `@RequestBody`와 달리 `@RequestParam` 주석은 int 및 String과 같은 간단한 데이터 유형만 지원한다.

따라서 JSON을 보내려면 JSON 매개변수를 간단한 문자열로 정의해야 한다.

JSON 매개변수(String)를 Product 클래스의 객체로 어떻게 변환하려면 Jackson 라이브러리에서 제공하는 ObjectMapper 클래스는 JSON 문자열을 Java 객체로 변환하는 유연한 방법을 제공 한다.

Spring MVC에서 GET 요청을 통해 JSON 매개 변수를 보내는 방법이다. 먼저 GET 요청을 처리하기 위해 컨트롤러에 다른 처리기 메서드를 만든다.

```java
@GetMapping("/get")
@ResponseBody
public Product getProduct(@RequestParam String product) throws JsonMappingException, JsonProcessingException {
    Product prod = objectMapper.readValue(product, Product.class);
    return prod;
}
```

`readValue()` 메서드를 사용하면 JSON 매개변수 product를 Product 클래스의 인스턴스로 직접 역 직렬화할 수 있다.

JSON 쿼리 매개변수를 String 개체로 정의한다. 이제 `@RequestBody`를 사용할 때 했던 것처럼 Product 객체를 전달하기 위해 Spring은 사용자 정의 [속성 편집기](https://www.baeldung.com/spring-mvc-custom-property-editor)를 통해 간결하고 유연한 솔루션을 제공한다.

먼저 문자열로 제공된 JSON 매개변수를 Product 개체로 변환하는 논리를 캡슐화하기 위해 사용자 지정 속성 편집기를 만든다.

```java
public class ProductEditor extends PropertyEditorSupport {

    private ObjectMapper objectMapper;

    public ProductEditor(ObjectMapper objectMapper) {
        this.objectMapper = objectMapper;
    }

    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        if (StringUtils.isEmpty(text)) {
            setValue(null);
        } else {
            Product prod = new Product();
            try {
                prod = objectMapper.readValue(text, Product.class);
            } catch (JsonProcessingException e) {
                throw new IllegalArgumentException(e);
            }
            setValue(prod);
        }
    }

}
```

다음으로 JSON 매개 변수를 Product 클래스의 개체에 바인딩한다.

```java
@GetMapping("/get2")
@ResponseBody
public Product get2Product(@RequestParam Product product) {
    // custom logic
    return product;
}
```

Spring 컨트롤러에 ProductEditor를 등록 한다.

```java
@InitBinder
public void initBinder(WebDataBinder binder) {
    binder.registerCustomEditor(Product.class, new ProductEditor(objectMapper));
}
```

안전한 전송을 위해 JSON 매개변수를 URL 인코딩해야 한다.

```text
GET /spring-mvc-basics-4/products/get2?product={"id": 1,"name": "Asus Zenbook","price": 800}
```

인코딩한 URL을 보낸다.

```text
GET /spring-mvc-basics-4/products/get2?product=%7B%22id%22%3A%201%2C%22name%22%3A%20%22Asus%20Zenbook%22%2C%22price%22%3A%20800%7D
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-mvc-send-json-parameters](https://www.baeldung.com/spring-mvc-send-json-parameters)
