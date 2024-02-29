---
title: Java Jackson Generic 유형 역직렬화
author: dejavuhyo
date: 2024-02-29 10:07:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-deserialize, generic-type-deserialize, 일반-유형-역직렬화]
---

## 1. 모델 준비
주어진 JSON 문자열을 역직렬화하려면 다음을 수행한다.

```json
{"result":{"id":1,"firstName":"John","lastName":"Lewis"}}
```

일반 유형 매개변수와 데이터를 보유하는 일반 POJO 객체를 사용하여 클래스를 정의해야 한다.

```java
public class JsonResponse<T> {
    private T result;

    // getters and setters...
}
```

```java
public class User {
    private Long id;
    private String firstName;
    private String lastName;

    // getters and setters...
}
```

## 2. 일반 유형 역직렬화
Jackson에서 ObjectMapper는 JSON 역직렬화를 위해 다음과 같은 세 가지 readValue 메서드 세트를 제공한다.

* 정보 유형을 전달하는 매개변수인 `Class<T>`

* 유형 정보를 전달하는 TypeReference

* 매개변수로 JavaType

`JsonResponse<User>.class`를 사용하여 첫 번째 글머리 기호부터 메서드에 전달할 수 없으므로 `TypeReference` 및 `JavaType`을 사용하여 제네릭 역직렬화를 수행한다.

### 1) 유형참조
잘 알려진 바와 같이 Java는 컴파일 중에 일반 유형 정보를 삭제하지만 익명 내부 클래스의 기능을 활용하여 컴파일 시간 동안 유형 정보를 보존할 수 있다. Jackson은 파생된 하위 클래스에서 유형 정보를 얻기 위해 추상 클래스 TypeReference를 제공한다.

```java
public abstract class TypeReference<T> {
    protected final Type _type;

    protected TypeReference() {
        Type superClass = this.getClass().getGenericSuperclass();
        this._type = ((ParameterizedType)superClass).getActualTypeArguments()[0]; 
    } 
}
```

TypeReference를 사용하면 다음과 같이 일반 유형 `JsonResponse<User>`에 대한 익명 내부 클래스를 만들 수 있다.

```java
TypeReference<JsonResponse<User>> typeRef = new TypeReference<JsonResponse<User>>() {};
```

일반 유형 정보를 보존하기 위한 이러한 접근 방식을 슈퍼 유형 토큰 이라고 한다. 슈퍼 유형 토큰을 사용함으로써 Jackson은 컨테이너 유형이 JsonResponse이고 해당 유형 매개변수가 User임을 알게 된다.

역직렬화에 대한 전체 테스트 사례는 다음과 같다.

```java
@Test
void givenJsonObject_whenDeserializeIntoGenericTypeByTypeReference_thenCorrect() throws JsonProcessingException {
    String json = "{\"result\":{\"id\":1,\"firstName\":\"John\",\"lastName\":\"Lewis\"}}";

    TypeReference<JsonResponse<User>> typeRef = new TypeReference<JsonResponse<User>>() {};
    JsonResponse<User> jsonResponse = objectMapper.readValue(json, typeRef);
    User user = jsonResponse.getResult();

    assertThat(user.getId()).isEqualTo(1);
    assertThat(user.getFirstName()).isEqualTo("John");
    assertThat(user.getLastName()).isEqualTo("Lewis");
}
```

### 2) 자바 유형
유형 매개변수 T가 정적이 아닌 경우 TypeReference 대신 JavaType을 선택하여 역직렬화를 위한 유형 정보를 전달 해야 한다. ObjectMapper는 현재 Jackson 2.5에서 권장되는 메소드를 제공하며 TypeFactory를 사용하여 유형 매개변수로 JavaType 객체를 구성 할 수 있다.

```java
JavaType javaType = objectMapper.getTypeFactory().constructParametricType(JsonResponse.class, User.class);
JsonResponse<User> jsonResponse = objectMapper.readValue(json, javaType);
```

여기서 `User.class`는 constructorParametricType 메소드의 두 번째 매개변수로 전달되며 다른 매개변수화된 유형으로 쉽게 변경할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-deserialize-generic-type-with-jackson](https://www.baeldung.com/java-deserialize-generic-type-with-jackson)
