---
title: Java Lombok을 이용하여 Jackson 역직렬화
author: dejavuhyo
date: 2024-03-11 14:11:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-deserialization, deserialization, jackson-lombok]
---

## 1. 종속성
시작하기 위해 필요한 것은 `pom.xml`에 추가된 [org.projectlombok](https://mvnrepository.com/artifact/org.projectlombok)이다.

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.30</version>
</dependency>
```

물론 [jackson-databind](https://mvnrepository.com/artifact/com.fasterxml.jackson.core) 종속성도 필요하다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.14.1</version>
</dependency>
```

## 2. 간단한 과일 도메인
과일을 나타내는 ID와 이름을 사용하여 Lombok 지원 클래스를 정의한다.

```java
@Data
@Builder
@Jacksonized
public class Fruit {
    private String name;
    private int id;
}
```

POJO의 주요 주석이다.

* 먼저 클래스에 `@Data` 주석을 추가하는 것부터 시작한다. 이는 일반적으로 getter 및 setter와 같은 간단한 POJO와 연관된 모든 상용구를 생성한다.

* 그런 다음 빌더 패턴을 사용하여 객체를 생성하는데 유용한 메커니즘인 `@Builder` 주석을 추가한다.

* 마지막으로 `@Jacksonized` 주석을 추가한다.

간단히 확장하자면 `@Jacksonized` 주석은 `@Builder`의 추가 기능 주석이다. 이 주석을 사용하면 생성된 빌더 클래스가 Jackson의 역직렬화와 작동하도록 자동으로 구성할 수 있다.

이 주석은 `@Builder` 또는 `@SuperBuilder` 주석이 있는 경우에만 작동한다.

마지막으로 `@Jacksonized`가 Lombok v1.18.14에 도입되었지만 아직은 실험적인 기능으로 간주된다.

## 3. 역직렬화 및 직렬화
이제 도메인 모델이 정의되었으므로 Jackson을 사용하여 과일을 역직렬화하는 단위 테스트를 작성한다.

```java
@Test
public void withFruitJSON_thenDeserializeSucessfully() throws IOException {
    String json = "{\"name\":\"Apple\",\"id\":101}";
        
    Fruit fruit = newObjectMapper().readValue(json, Fruit.class);
    assertEquals(new Fruit("Apple", 101), fruit);
}
```

ObjectMapper의 간단한 `readValue()` API로 충분하다. 이를 사용하여 JSON 과일 문자열을 Fruit Java 개체로 역직렬화할 수 있다.

마찬가지로 `writeValue()` API를 사용하여 Fruit 객체를 JSON 출력으로 직렬화 할 수 있다.

```java
@Test
void withFruitObject_thenSerializeSucessfully() throws IOException {
    Fruit fruit = Fruit.builder()
      .id(101)
      .name("Apple")
      .build();

    String json = newObjectMapper().writeValueAsString(fruit);
    assertEquals("{\"name\":\"Apple\",\"id\":101}", json);
}
```

테스트에서는 Lombok 빌더 API를 사용하여 Fruit을 빌드하는 방법과 직렬화된 Java 객체가 예상 JSON 문자열과 일치하는지 보여준다.

## 4. 맞춤형 빌더로 작업하기
때때로 Lombok이 생성한 것이 아닌 사용자 정의된 빌더 구현으로 작업해야 할 수도 있다. 예를 들어, Bean의 속성 이름이 JSON 문자열의 필드 이름과 다른 경우이다.

다음 JSON 문자열을 역직렬화한다고 가정한다.

```json
{
    "id": 5,
    "name": "Bob"
}
```

그러나 POJO의 속성이 일치하지 않는다.

```java
@Data
@Builder(builderClassName = "EmployeeBuilder")
@JsonDeserialize(builder = Employee.EmployeeBuilder.class)
@AllArgsConstructor
public class Employee {

    private int identity;
    private String firstName;

}
```

이 시나리오에서는 `@JsonPOJOBuilder` 주석과 함께 `@JsonDeserialize` 주석을 사용할 수 있다. 이를 생성된 빌더 클래스에 삽입하여 Jackson의 기본값을 재정의할 수 있다.

```java
@JsonPOJOBuilder(buildMethodName = "createEmployee", withPrefix = "construct")
public static class EmployeeBuilder {

    private int idValue;
    private String nameValue;

    public EmployeeBuilder constructId(int id) {
        idValue = id;
        return this;
    }
            
    public EmployeeBuilder constructName(String name) {
        nameValue = name;
        return this;
    }

    public Employee createEmployee() {
        return new Employee(idValue, nameValue);
    }
}
```

그런 다음 이전과 같이 테스트를 작성할 수 있다.

```java
@Test
public void withEmployeeJSON_thenDeserializeSucessfully() throws IOException {
    String json = "{\"id\":5,\"name\":\"Bob\"}";
    Employee employee = newObjectMapper().readValue(json, Employee.class);

    assertEquals(5, employee.getIdentity());
    assertEquals("Bob", employee.getFirstName());
}
```

결과는 속성 이름이 일치하지 않음에도 불구하고 JSON 소스에서 새 직원 데이터 개체가 성공적으로 다시 생성되었음을 보여준다.

## [출처 및 참고]
* [https://www.baeldung.com/java-jackson-deserialization-lombok](https://www.baeldung.com/java-jackson-deserialization-lombok)
