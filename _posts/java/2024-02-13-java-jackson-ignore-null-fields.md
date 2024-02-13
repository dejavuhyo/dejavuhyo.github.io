---
title: Java Jackson Null 필드 무시
author: dejavuhyo
date: 2024-02-13 10:20:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-null, ignore-null-fields, null-필드, null-필드-무시]
---

## 1. 클래스의 Null 필드 무시
Jackson을 사용하면 클래스 수준에서 이 동작을 제어할 수 있다.

```java
@JsonInclude(Include.NON_NULL)
public class MyDto { ... }
```

또는 필드 수준에서 더욱 세부적으로 동작을 제어할 수 있다.

```java
public class MyDto {

    @JsonInclude(Include.NON_NULL)
    private String stringValue;

    private int intValue;

    // standard getters and setters
}
```

null 값이 실제로 최종 JSON 출력의 일부가 아닌지 테스트할 수 있다.

```java
@Test
public void givenNullsIgnoredOnClass_whenWritingObjectWithNullField_thenIgnored()
  throws JsonProcessingException {
    ObjectMapper mapper = new ObjectMapper();
    MyDto dtoObject = new MyDto();

    String dtoAsString = mapper.writeValueAsString(dtoObject);

    assertThat(dtoAsString, containsString("intValue"));
    assertThat(dtoAsString, not(containsString("stringValue")));
}
```

## 2. 전역적으로 Null 필드 무시
Jackson을 사용하면 ObjectMapper에서 전역적으로 이 동작을 구성 할 수 있다.

```java
mapper.setSerializationInclusion(Include.NON_NULL);
```

이 매퍼를 통해 직렬화된 모든 클래스의 모든 null 필드는 무시된다.

```java
@Test
public void givenNullsIgnoredGlobally_whenWritingObjectWithNullField_thenIgnored() 
  throws JsonProcessingException {
    ObjectMapper mapper = new ObjectMapper();
    mapper.setSerializationInclusion(Include.NON_NULL);
    MyDto dtoObject = new MyDto();

    String dtoAsString = mapper.writeValueAsString(dtoObject);

    assertThat(dtoAsString, containsString("intValue"));
    assertThat(dtoAsString, containsString("booleanValue"));
    assertThat(dtoAsString, not(containsString("stringValue")));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/jackson-ignore-null-fields](https://www.baeldung.com/jackson-ignore-null-fields)
