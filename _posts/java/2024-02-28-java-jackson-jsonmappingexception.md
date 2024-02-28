---
title: Java JsonMappingException - Can not deserialize instance of java.util.ArrayList from Object value (token JsonToken.START_OBJECT)
author: dejavuhyo
date: 2024-02-28 10:14:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-jsonmappingexception, jackson-exception, jackson-오류]
---

## 1. Exception 이해
일반적으로 Jackson은 JSON 문자열을 역직렬화할 때 치명적인 매핑 오류를 알리기 위해 JsonMappingException을 발생시킨다. 따라서 stack trace "Can not deserialize instance of java.util.ArrayList"는 Jackson이 JSON 속성을 ArrayList 인스턴스에 매핑하지 못했음을 나타낸다.

간단히 말해서, 이 exception의 가장 일반적인 원인은 대괄호 `[...]` 대신 컬렉션을 나타내기 위해 중괄호 `{...}`를 사용하는 것이다.

## 2. Exception 재현
Jackson이 JsonMappingException을 발생시키는 원인을 알았으므로 실제 예를 사용하여 이를 재현하는 방법이다.

Country 클래스를 생성한다.

```java
public class Country {

    private String name;
    private List<String> cities;

    // standard getters and setters
}
```

보시다시피 국가는 이름과 도시 목록으로 정의된다.

다음으로 중괄호를 사용하여 JSON 문자열에서 도시를 정의한다고 가정한다.

```json
{
    "name": "Netherlands", 
    "cities": {"Amsterdam", "Tamassint"}
}
```

이제 JSON 문자열을 국가 유형의 개체로 역직렬화하려고 하면 다음과 같은 결과가 발생한다.

```text
Cannot deserialize value of type `java.util.ArrayList<java.lang.String>` from Object value (token `JsonToken.START_OBJECT`)
at [Source: (String)"{"name":"Netherlands","cities":{"Amsterdam", "Tamassint"}}"; line: 1, column: 32] 
(through reference chain: com.baeldung.mappingexception.Country["cities"])
...
```

마지막으로 이를 확인하기 위해 테스트 사례를 만든다.

```java
@Test
public final void givenJsonWithInvalidList_whenDeserializing_thenThrowException() throws JsonParseException, IOException {
    String json = "{\"name\":\"Netherlands\",\"cities\":{\"Amsterdam\", \"Tamassint\"}}";
    ObjectMapper mapper = new ObjectMapper();

    Exception exception = assertThrows(JsonMappingException.class, () -> mapper.reader()
      .forType(Country.class)
      .readValue(json));

    assertTrue(exception.getMessage()
      .contains("Cannot deserialize value of type `java.util.ArrayList<java.lang.String>`"));
}
```

위에 표시된 것처럼 Jackson은 `Cannot deserialize value of type 'java.util.ArrayList<java.lang.String>'`라는 메시지와 함께 실패한다.

여기서 주된 이유는 도시 목록을 나타내기 위해 중괄호를 사용했기 때문이다. Jackson의 경우 `{"Amsterdam", "Tamassint"}`는 JSON 배열이 아니다.

## 3. Exception 수정
예외를 피하는 가장 쉬운 방법은 중괄호 대신 대괄호를 사용하여 요소 컬렉션을 정의하는 것이다. 따라서 예외를 해결하려면 먼저 JSON 문자열을 수정해야 한다.

```java
{ 
    "name": "Netherlands",
    "cities": ["Amsterdam", "Tamassint"]
}
```

이제 테스트를 사용하여 모든 것이 예외적으로 작동하는지 확인한다.

```java
@Test
public final void givenJsonWithValidList_whenDeserializing_thenCorrect() throws JsonParseException, IOException {
    String json = "{\"name\":\"Netherlands\",\"cities\":[\"Amsterdam\", \"Tamassint\"]}";
    ObjectMapper mapper = new ObjectMapper();

    Country country = mapper.reader()
      .forType(Country.class)
      .readValue(json);

    assertEquals("Netherlands", country.getName());
    assertEquals(Arrays.asList("Amsterdam", "Tamassint"), country.getCities());
}
```

새 JSON 문자열은 Country 개체로 성공적으로 역직렬화되었다.

## [출처 및 참고]
* [https://www.baeldung.com/jsonmappingexception-can-not-deserialize-instance-of-java-util-arraylist-from-object-value-token-jsontoken-start_object](https://www.baeldung.com/jsonmappingexception-can-not-deserialize-instance-of-java-util-arraylist-from-object-value-token-jsontoken-start_object)
