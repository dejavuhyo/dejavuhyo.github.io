---
title: Java Jackson 동적 JSON 매핑
author: dejavuhyo
date: 2024-03-15 13:22:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-dynamic-json, json-mapping]
---

## 1. JsonNode 사용
웹샵에서 제품 사양을 처리하고 싶다고 가정한다. 모든 제품에는 몇 가지 공통된 특성이 있지만 제품 유형에 따라 다른 특성도 있다.

예를 들어, 휴대폰 디스플레이의 종횡비를 알고 싶지만 신발에는 이 속성이 별로 의미가 없다.

데이터 구조는 다음과 같다.

```json
{
    "name": "Pear yPhone 72",
    "category": "cellphone",
    "details": {
        "displayAspectRatio": "97:3",
        "audioConnector": "none"
    }
}
```

세부정보 개체에 동적 속성을 저장한다.

다음 Java 클래스를 사용하여 공통 속성을 매핑할 수 있다.

```java
class Product {

    String name;
    String category;

    // standard getters and setters
}
```

게다가 세부정보 개체에 대한 적절한 표현이 필요하다. 예를 들어 `com.fasterxml.jackson.databind.JsonNode`는 동적 키를 처리할 수 있다.

이를 사용하려면 Product 클래스에 필드로 추가해야 한다.

```java
class Product {

    // common fields

    JsonNode details;

    // standard getters and setters
}
```

마지막으로 작동하는지 확인한다.

```java
String json = "<json object>";

Product product = objectMapper.readValue(json, Product.class);

assertThat(product.getName()).isEqualTo("Pear yPhone 72");
assertThat(product.getDetails().get("audioConnector").asText()).isEqualTo("none");
```

그러나 이 솔루션에는 문제가 있다. 클래스는 JsonNode 필드가 있으므로 Jackson 라이브러리에 의존한다.

## 2. Map 사용
세부정보 필드에 `java.util.Map`을 사용하면 이 문제를 해결할 수 있다. 보다 정확하게는 `Map<String, Object>`를 사용해야 한다.

다른 모든 것은 동일하게 유지될 수 있다.

```java
class Product {

    // common fields

    Map<String, Object> details;

    // standard getters and setters
}
```

그런 다음 테스트를 통해 이를 확인할 수 있다.

```java
String json = "<json object>";

Product product = objectMapper.readValue(json, Product.class);

assertThat(product.getName()).isEqualTo("Pear yPhone 72");
assertThat(product.getDetails().get("audioConnector")).isEqualTo("none");
```

## 3. @JsonAnySetter 사용
이전 솔루션은 객체에 동적 속성만 포함된 경우 좋은 옵션이다. 그러나 때로는 단일 JSON 객체에 고정 속성과 동적 속성이 혼합되어 있는 경우도 있다.

예를 들어 제품 표현을 평면화해야 할 수도 있다.

```json
{
    "name": "Pear yPhone 72",
    "category": "cellphone",
    "displayAspectRatio": "97:3",
    "audioConnector": "none"
}
```

이런 종류의 구조를 동적 객체로 처리할 수 있다. 불행하게도 이는 공통 속성을 정의할 수 없으며 동적으로 처리해야 함을 의미한다.

또는 `@JsonAnySetter`를 사용하여 알 수 없는 추가 속성을 처리하기 위한 메서드를 표시 할 수 있다. 이러한 메서드는 속성의 이름과 값이라는 두 가지 인수를 받아들여야 한다.

```java
class Product {

    // common fields

    Map<String, Object> details = new LinkedHashMap<>();

    @JsonAnySetter
    void setDetail(String key, Object value) {
        details.put(key, value);
    }

    // standard getters and setters
}
```

NullPointerExceptions를 방지하려면 세부정보 개체를 인스턴스화해야 한다.

동적 속성을 Map에 저장하므로 이전과 동일한 방식으로 사용할 수 있다.

```java
String json = "<json object>";

Product product = objectMapper.readValue(json, Product.class);

assertThat(product.getName()).isEqualTo("Pear yPhone 72");
assertThat(product.getDetails().get("audioConnector")).isEqualTo("none");
```

## [출처 및 참고]
* [https://www.baeldung.com/jackson-mapping-dynamic-object](https://www.baeldung.com/jackson-mapping-dynamic-object)
