---
title: Java Jackson 필드 이름 변경
author: dejavuhyo
date: 2024-03-21 09:57:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-change-field, jackson-field, 필드-이름-변경]
---

## 1. 직렬화 필드명 변경
단순 엔터티 작업이다.

```java
public class MyDto {
    private String stringValue;

    public MyDto() {
        super();
    }

    public String getStringValue() {
        return stringValue;
    }

    public void setStringValue(String stringValue) {
        this.stringValue = stringValue;
    }
}
```

직렬화하면 다음 JSON이 생성된다.

```json
{"stringValue":"some value"}
```

stringValue 대신에 strVal을 얻도록 해당 출력을 사용자 정의하려면 간단히 getter에 주석을 달아야 한다.

```java
@JsonProperty("strVal")
public String getStringValue() {
    return stringValue;
}
```

이제 직렬화 시 원하는 출력을 얻을 수 있다.

```json
{"strVal":"some value"}
```

간단한 단위 테스트를 통해 출력이 올바른지 확인한다.

```java
@Test
public void givenNameOfFieldIsChanged_whenSerializing_thenCorrect() 
  throws JsonParseException, IOException {
    ObjectMapper mapper = new ObjectMapper();
    MyDtoFieldNameChanged dtoObject = new MyDtoFieldNameChanged();
    dtoObject.setStringValue("a");

    String dtoAsString = mapper.writeValueAsString(dtoObject);

    assertThat(dtoAsString, not(containsString("stringValue")));
    assertThat(dtoAsString, containsString("strVal"));
}
```

## [출처 및 참고]
* [https://www.baeldung.com/jackson-name-of-property](https://www.baeldung.com/jackson-name-of-property)
