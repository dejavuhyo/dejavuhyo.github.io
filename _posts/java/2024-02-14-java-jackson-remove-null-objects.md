---
title: Java Jackson에서 JSON Null Objects 제거
author: dejavuhyo
date: 2024-02-14 11:04:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-null, jackson-remove-null, 잭슨-null-제거, 잭슨-null]
---

## 1. Null Values
JSON은 RESTful 애플리케이션의 사실상 표준이다. Spring은 Jackson 라이브러리를 사용하여 객체를 JSON으로 원활하게 변환한다. 그러나 때로는 변환을 사용자 정의하고 특정 규칙을 제공하고 싶을 수도 있다.

그러한 것 중 하나는 응답이나 요청에서 비어 있거나 null 값을 무시하는 것이다. 이는 빈 값을 주고받을 필요가 없으므로 성능상의 이점을 제공할 수 있다. 또한 이는 API를 더욱 간단하게 만들 수 있다.

요청을 보내거나 받는 동안 값이 null로 설정된 것을 자주 볼 수 있다. 그러나 일반적으로 이는 정의되지 않은 변수나 필드의 기본값이므로 유용한 정보를 제공하지 않는다.

또한 JSON에 전달된 null 값을 허용한다는 사실로 인해 유효성 검사 프로세스가 복잡해진다. 값이 없으면 유효성 검사를 건너뛰고 기본값으로 설정할 수 있다. 그러나 값이 존재하는 경우 해당 값이 null 인지 , 그리고 이를 합리적인 표현으로 변환할 수 있는지 확인하기 위해 추가 검사를 수행해야 한다.

Jackson은 클래스에서 직접 구성할 수 있는 편리한 방법을 제공한다. [Include.NON_NULL](https://www.baeldung.com/jackson-ignore-null-fields)을 사용한다. 규칙이 모든 필드에 적용되는 경우 클래스 수준에서 사용할 수 있으며, fields, getter 및 setter에서 더 세부적으로 사용할 수 있다.

```java
@JsonInclude(Include.NON_NULL)
public class Employee {
    private String lastName;
    private String firstName;
    private long id;
    // constructors, getters and setters
}
```

필드 중 하나라도 null 이고 참조 필드에 대해서만 이야기하는 경우 해당 필드는 생성된 JSON에 포함되지 않는다.

```java
@ParameterizedTest
@MethodSource
void giveEndpointWhenSendEmployeeThanReceiveThatUserBackIgnoringNullValues(Employee expected) throws Exception {
    MvcResult result = sendRequestAndGetResult(expected, USERS);
    String response = result.getResponse().getContentAsString();
    validateJsonFields(expected, response);
}

private void validateJsonFields(Employee expected, String response) throws JsonProcessingException {
    JsonNode jsonNode = mapper.readTree(response);
    Predicate<Field> nullField = s -> isFieldNull(expected, s);
    List<String> nullFields = filterFieldsAndGetNames(expected, nullField);
    List<String> nonNullFields = filterFieldsAndGetNames(expected, nullField.negate());
    nullFieldsShouldBeMissing(nullFields, jsonNode);
    nonNullFieldsShouldNonBeMissing(nonNullFields, jsonNode);
}
```

때때로 null 유사 필드에 대해 유사한 동작을 복제하기를 원하며 Jackson은 이를 처리하는 방법도 제공한다.

## 2. Absent Values
빈 [Optional](https://www.baeldung.com/java-optional)은 기술적으로 null이 아닌 값이다. 그러나 요청이나 응답에서 존재하지 않는 값에 대한 래퍼를 전달하는 것은 의미가 없다. 이전 주석은 이 경우를 처리하지 않으며 래퍼 자체에 대한 일부 정보를 추가하려고 시도한다.

```json
{
  "lastName": "John",
  "firstName": "Doe",
  "id": 1,
  "salary": {
    "empty": true,
    "present": false
  }
}
```

우리 회사의 모든 직원이 원할 경우 급여를 공개할 수 있다고 가정한다.

```java
@JsonInclude(Include.NON_ABSENT)
public class Employee {
    private String lastName;
    private String firstName;
    private long id;
    private Optional<Salary> salary;
    // constructors, getters and setters
}
```

null 값을 반환하는 사용자 정의 getter 및 setter를 사용하여 이를 처리할 수 있다. 그러나 이렇게 하면 API가 복잡해지고 처음에 Optional을 사용하는 이면의 아이디어가 무시된다. 비어 있는 Optionals를 무시하려면 `Include.NON_ABSENT`를 사용할 수 있다.

```java
private void validateJsonFields(Employee expected, String response) throws JsonProcessingException {
    JsonNode jsonNode = mapper.readTree(response);
    Predicate<Field> nullField = s -> isFieldNull(expected, s);
    Predicate<Field> absentField = s -> isFieldAbsent(expected, s);
    List<String> nullOrAbsentFields = filterFieldsAndGetNames(expected, nullField.or(absentField));
    List<String> nonNullAndNonAbsentFields = filterFieldsAndGetNames(expected, nullField.negate().and(absentField.negate()));
    nullFieldsShouldBeMissing(nullOrAbsentFields, jsonNode);
    nonNullFieldsShouldNonBeMissing(nonNullAndNonAbsentFields, jsonNode);
}
```

`Include.NON_ABSENT`는 빈 Optional 값과 null을 처리 하므로 두 시나리오 모두에 사용할 수 있다.

## 3. Empty Values
생성된 JSON에 빈 문자열이나 빈 컬렉션을 포함하는 대부분의 경우에는 말이 되지 않는다. null로 설정하거나 Optionals로 래핑하는 것은 좋은 생각이 아닐 수 있으며 객체와의 상호 작용을 복잡하게 만들 수 있다.

직원에 대한 몇 가지 추가 정보를 고려한다. 우리가 국제 조직에서 일하고 있기 때문에 직원이 자신의 이름에 발음 기호를 추가하고 싶어할 수도 있다고 가정하는 것이 합리적이다. 또한 다른 사람이 연락할 수 있도록 전화번호를 제공할 수도 있다.

```java
@JsonInclude(Include.NON_EMPTY)
public class Employee {
    private String lastName;
    private String firstName;
    private long id;
    private Optional<Salary> salary;
    private String phoneticName = "";
    private List<PhoneNumber> phoneNumbers = new ArrayList<>();
    // constructors, getters and setters
}
```

값이 비어 있으면 `Include.NON_EMPTY`를 사용하여 값을 제외할 수 있다. 이 구성은 null 및 absent values도 무시한다.

```java
private void validateJsonFields(Employee expected, String response) throws JsonProcessingException {
    JsonNode jsonNode = mapper.readTree(response);
    Predicate<Field> nullField = s -> isFieldNull(expected, s);
    Predicate<Field> absentField = s -> isFieldAbsent(expected, s);
    Predicate<Field> emptyField = s -> isFieldEmpty(expected, s);
    List<String> nullOrAbsentOrEmptyFields = filterFieldsAndGetNames(expected, nullField.or(absentField).or(emptyField));
    List<String> nonNullAndNonAbsentAndNonEmptyFields = filterFieldsAndGetNames(expected,
      nullField.negate().and(absentField.negate().and(emptyField.negate())));
    nullFieldsShouldBeMissing(nullOrAbsentOrEmptyFields, jsonNode);
    nonNullFieldsShouldNonBeMissing(nonNullAndNonAbsentAndNonEmptyFields, jsonNode);
}
```

이러한 모든 주석은 보다 세부적으로 사용될 수 있으며 다양한 분야에 다양한 전략을 적용할 수도 있다. 또한 이 규칙을 모든 변환에 적용하도록 매퍼를 전역적으로 구성할 수 있다.

## 4. Custom Mappers
위의 전략이 요구에 충분히 유연하지 않거나 특정 규칙을 지원해야 하는 경우에는 `Include.CUSTOM`을 사용하거나 [custom serializer](https://www.baeldung.com/jackson-custom-serialization)를 구현 해야 한다.

```java
public class CustomEmployeeSerializer extends StdSerializer<Employee> {
    @Override
    public void serialize(Employee employee, JsonGenerator gen, SerializerProvider provider)
      throws IOException {
        gen.writeStartObject();
        // Custom logic to serialize other fields
        gen.writeEndObject();
    }
}
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-remove-null-objects-json-response-jackson](https://www.baeldung.com/spring-remove-null-objects-json-response-jackson)
