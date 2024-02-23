---
title: Java Jackson 매핑 기본값 Null 설정
author: dejavuhyo
date: 2024-02-23 10:59:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-null, jackson-default-values, default-null]
---

## 1. 클래스 수준에서 기본값 설정
첫 번째 예는 들어오는 JSON String에서 POJO가 완전히 누락되었을때 POJO에 기본값을 가져올 수 있는 방법이다. 두 개의 필드(필수 필드와 기본값을 설정할 필드)가 있는 객체를 생성한다.

```java
class NonAnnotatedDefaultValue {
    String required;
    String optional = "defaultValue";
    // Standard getters and setters
}
```

여기서는 선택 사항 이라는 필드에 값을 할당했다. 그러면 JSON에 필드가 누락된 경우 Jackson이 해당 문자열을 사용하게 된다. 이제 Jackson에게 선택 사항 이라는 필드가 없는  JSON 문자열을 매핑하도록 요청하여 해당 객체를 사용한다. ObjectMapper 개체와 해당 `readValue()` 메서드를 사용한다.

```java
@Test
void givenAClassWithADefaultValue_whenReadingJsonWithoutOptionalValue_thenExpectDefaultValueInResult()
  throws JsonProcessingException {
    String noOptionalField = "{\"required\": \"value\"}";
    ObjectMapper objectMapper = new ObjectMapper();
    NonAnnotatedDefaultValue createdObject = objectMapper.readValue(noOptionalField, NonAnnotatedDefaultValue.class);
    assert(createdObject.getRequired()).equals("value");
    assert(createdObject.getOptional()).equals("defaultValue");
}
```

결과 개체에 JSON의 값과 이전에 지정한 기본값이 모두 포함되어 있다.

이 방법의 단점은 속성이 수신 JSON에서 완전히 누락된 경우에만 작동한다는 것이다. 속성에 null 값이 있으면 Jackson은 기본값을 적용하지 않는다.

## 2. 최대 제어를 위한 Setter 메소드 구현
필드에 대한 setter 메소드를 구현하여 매핑 프로세스를 완벽하게 제어할 수 있다. Jackson이 객체를 생성할 때 사용할 필수 setter를 사용하여 객체를 생성한다.

```java
class SetterDefaultValue {
   String required;
   String optional = "valueIfMissingEntirely";

    public void setOptional(String optional) {
        if (optional == null) {
            this.optional = "valueIfNull";
        }
    }
   // Standard getters and setters
}
```

이전과 같이 클래스 수준에서 기본값을 제공했다. 그러나 setter 메소드도 제공했다. 이제 해당 setter 메서드 내에서 원하는 모든 작업을 수행할 수 있다. 이 예에서는 값이 null 인 경우 예상되는 동작을 명시적으로 제공했다. 이제 Jackson은 JSON에 속성을 포함하지 않은 경우 valueIfMissingEntirely를 선택 사항으로 설정하고, valueIfNull은 포함했지만 null로 설정해야 한다.

요구 사항을 충족한다면 두 값을 모두 동일하게 만들 수 있다.

```java
@Test
void givenAClassWithASetter_whenReadingJsonWithNullOptionalValue_thenExpectDefaultValueInResult()
  throws JsonProcessingException {
    String nullOptionalField = "{\"required\": \"value\", \"optional\": null}";
    ObjectMapper objectMapper = new ObjectMapper();
    JsonSetterDefaultValue createdObject = objectMapper.readValue(nullOptionalField, JsonSetterDefaultValue.class);
    assert(createdObject.getRequired()).equals("value");
    assert(createdObject.getOptional()).equals("valueIfNull");
}
```

여기서는 option 이라는 필드가 null로 설정된 JSON을 제공했다. Jackson이 생성한 결과 개체에서 주석 덕분에 필드가 valueIfNull로 설정되었다는 어설션을 통해 알 수 있다. 이 옵션은 접근 방식에 엄청난 유연성을 제공한다. 또한 빈 String을 확인하고 원하는 경우 동일한 방식으로 기본값을 적용할 수도 있다.

## 3. Nulls.SKIP과 @JsonSetter 사용
마지막 옵션은 `@JsonSetter`를 활용하고 null을 무시하도록 사용법을 확장한다. 새 클래스를 만든다.

```java
class NullsSkipDefaultValue {
    private String required;
    @JsonSetter(nulls = Nulls.SKIP)
    private String optional = "defaultValue";
    // standard getters and setters
}
```

`Nulls.SKIP` 인수는 `@JsonSetter`에게 null 값이 있는 입력을 건너뛰도록 지시한다. 그런 다음 Jackson은 제공된 기본값을 대신 사용한다. Nulls 열거형에는 사용할 수 있는 몇 가지 다른 옵션이 있다. 예를 들어 `Nulls.SET`은 Jackson에게 JSON의 null을 POJO의 Java null로 변환하도록 지시한다.

```java
@Test
void givenAClassWithAJsonSetterNullsSkip_whenReadingJsonWithNullOptionalValue_thenExpectDefaultValueInResult() throws JsonProcessingException {
    String nullOptionalField = "{\"required\": \"value\", \"optional\": null}";
    ObjectMapper objectMapper = new ObjectMapper();
    NullsSkipDefaultValue createdObject = objectMapper.readValue(nullOptionalField, NullsSkipDefaultValue.class);
    assert(createdObject.getRequired()).equals("value");
    assert(createdObject.getOptional()).equals("defaultValue");
}
```

위에서는 선택적 필드가 null에 할당된 입력 JSON이 주어 지면 원하는 기본값을 얻는다는 것을 알 수 있다. 선택적 필드를 제공하지 않는 경우에도 동일한 결과가 제공된다.

## [출처 및 참고]
* [https://www.baeldung.com/java-jackson-mapping-default-values-null-fields](https://www.baeldung.com/java-jackson-mapping-default-values-null-fields)
