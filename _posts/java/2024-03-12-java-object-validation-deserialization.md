---
title: Java 역직렬화 후 객체 유효성 검사
author: dejavuhyo
date: 2024-03-12 10:10:00 +0900
categories: [Language, Java]
tags: [java-validation, object-validation, deserialization, 객체-유효성, 유효성-검사]
---

## 1. 수동 검증 트리거
Bean 유효성 검증을 위한 Java API는 [JSR 380](https://jcp.org/en/jsr/detail?id=380)에 정의되어 있다. 일반적인 용도는 Spring 컨트롤러의 `@Valid` 주석이 달린 매개변수이다.

먼저 개체의 내용이 유효성 검사 제약 조건을 준수하는지 확인하는 메서드를 작성한다. 이를 위해 기본 유효성 검사기 팩토리에서 유효성 검사기를 가져온다. 그런 다음 개체에 유효성 `validate()` 메서드를 적용한다. 이 메서드는 ConstraintViolation 세트를 반환한다. ConstraintViolation은 유효성 검사 오류에 대한 몇 가지 힌트를 캡슐화한다. 간단하게 유지하기 위해 유효성 검사 문제가 발생할 경우 ConstraintViolationException을 발생시킨다.

```java
<T> void validate(T t) {
    Set<ConstraintViolation<T>> violations = validator.validate(t);
    if (!violations.isEmpty()) {
        throw new ConstraintViolationException(violations);
    }
}
```

객체에 대해 이 메서드를 호출하면 객체가 유효성 검사 제약 조건을 따르지 않으면 오류가 발생한다. 이 메서드는 제약 조건이 연결된 기존 개체의 어느 지점에서나 호출할 수 있다.

## 2. 유효성 검사 역직렬화 프로세스 통합
목표는 유효성 검사를 역직렬화 프로세스에 통합하는 것이다. 구체적으로, 역직렬화 직후에 유효성 검사를 수행하기 위해 Jackson의 역직렬 변환기를 재정의할 것이다. 이렇게 하면 객체를 역직렬화할 때마다 객체가 규격을 준수하지 않는 경우 더 이상의 처리를 허용하지 않는다.

먼저 기본 BeanDeserializer를 재정의해야 한다. BeanDeserializer는 객체를 역직렬화할 수 있는 클래스이다. 기본 역직렬화 메서드를 호출한 다음 생성된 인스턴스에 유효성 `validate()` 메서드를 적용한다. BeanDeserializerWithValidation은 다음과 같다.

```java
public class BeanDeserializerWithValidation extends BeanDeserializer {

    protected BeanDeserializerWithValidation(BeanDeserializerBase src) {
        super(src);
    }

    @Override
    public Object deserialize(JsonParser p, DeserializationContext ctxt) throws IOException {
        Object instance = super.deserialize(p, ctxt);
        validate(instance);
        return instance;
    }

}
```

다음 단계는 자체 BeanDeserializerModifier를 구현하는 것이다. 이를 통해 BeanDeserializerWithValidation에 정의된 동작으로 역직렬화 프로세스를 변경할 수 있다.

```java
public class BeanDeserializerModifierWithValidation extends BeanDeserializerModifier {

    @Override
    public JsonDeserializer<?> modifyDeserializer(DeserializationConfig config, BeanDescription beanDesc, JsonDeserializer<?> deserializer) {
        if (deserializer instanceof BeanDeserializer) {
            return new BeanDeserializerWithValidation((BeanDeserializer) deserializer);
        }

        return deserializer;
    }

}
```

마지막으로 ObjectMapper를 생성하고 BeanDeserializerModifier를 Module로 등록 해야 한다. 모듈은 Jackson의 기본 기능을 확장하는 방법이다. 이를 메소드로 포장한다.

```java
ObjectMapper getObjectMapperWithValidation() {
    SimpleModule validationModule = new SimpleModule();
    validationModule.setDeserializerModifier(new BeanDeserializerModifierWithValidation());
    ObjectMapper mapper = new ObjectMapper();
    mapper.registerModule(validationModule);
    return mapper;
}
```

## 3. 사용 예
사용자 정의 ObjectMapper를 사용하는 방법에 대한 예이다. 먼저 Student 개체를 정의한다. Student에게는 이름이 있다. 이름 길이는 5~10자 사이여야 한다.

```java
public class Student {

    @Size(min = 5, max = 10, message = "Student's name must be between 5 and 10 characters")
    private String name;

    public String getName() {
        return name;
    }

}
```

이제 유효한 Student 개체의 JSON 표현을 포함하는 `validStudent.json` 파일을 만든다.

```json
{
  "name": "Daniel"
}
```

InputStream에서 이 파일의 내용을 읽을 것이다. 먼저, InputStream을 Student 객체로 구문 분석 하고 동시에 유효성을 검사하는 메서드를 정의한다. 이를 위해 ObjectMapper를 사용한다.

```java
Student readStudent(InputStream inputStream) throws IOException {
    ObjectMapper mapper = getObjectMapperWithValidation();
    return mapper.readValue(inputStream, Student.class);
}
```

이제 다음과 같은 테스트를 작성할 수 있다.

* 파일 내용을 InputStream으로 읽는 것부터 시작한다.

* InputStream을 Student 객체로 변환한다.

* Student 객체의 내용이 예상한 내용과 일치하는지 확인한다.

이 테스트는 다음과 같다.

```java
@Test
void givenValidStudent_WhenReadStudent_ThenReturnStudent() throws IOException {
    InputStream inputStream = getClass().getClassLoader().getResourceAsStream(("validStudent.json");
    Student result = readStudent(inputStream);
    assertEquals("Daniel", result.getName());
}
```

마찬가지로 이름이 5자 미만인 Student의 JSON 표현을 포함하는 `valid.json` 파일을 생성할 수 있다.

```json
{
  "name": "Max"
}
```

이제 ConstraintViolationException이 실제로 발생했는지 확인하기 위해 테스트를 조정해야 한다. 또한 오류 메시지가 올바른지 확인할 수 있다.

```java
@Test
void givenStudentWithInvalidName_WhenReadStudent_ThenThrows() {
    InputStream inputStream = getClass().getClassLoader().getResourceAsStream("invalidStudent.json");
    ConstraintViolationException constraintViolationException = assertThrows(ConstraintViolationException.class, () -> readStudent(inputStream));
    assertEquals("name: Student's name must be between 5 and 10 characters", constraintViolationException.getMessage());
}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-object-validation-deserialization](https://www.baeldung.com/java-object-validation-deserialization)
