---
title: Java Jackson Optional
author: dejavuhyo
date: 2024-03-19 09:37:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-optional]
---

## 1. 문제
Jackson을 사용하여 Optional을 직렬화 및 역직렬화하려고 할 때 어떤 일이 발생하는지 확인한다.

### 1)  메이븐 의존성
[Jackson](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core)을 사용하려면 최신 버전을 사용하고 있는지 확인한다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.13.3</version>
</dependency>
```

### 2) Book Object
하나의 일반 필드와 하나의 선택적 필드를 포함하는 Book 클래스를 만든다.

```java
public class Book {
   String title;
   Optional<String> subTitle;
   
   // getters and setters omitted
}
```

Optional은 필드로 사용되어서는 안되며 문제를 설명하기 위해 이 작업을 수행한다.

### 3) 직렬화
Book을 인스턴스화한다.

```java
Book book = new Book();
book.setTitle("Oliver Twist");
book.setSubTitle(Optional.of("The Parish Boy's Progress"));
```

마지막으로 Jackson ObjectMapper를 사용하여 직렬화한다.

```java
String result = mapper.writeValueAsString(book);
```

Optional 필드의 출력에는 해당 값이 포함되지 않고 대신 Present라는 필드가 있는 중첩된 JSON 개체가 포함되어 있다.

```json
{"title":"Oliver Twist","subTitle":{"present":true}}
```

이 경우 `isPresent()`는 Optional 클래스의 공개 getter이다. 즉, 비어 있는지 여부에 따라 true 또는 false 값으로 직렬화된다. 이는 Jackson의 기본 직렬화 동작이다.

원하는 것은 자막 필드의 값이 실제로 직렬화되는 것이다.

### 4) 역직렬화
이제 이전 예제를 반대로 하여 이번에는 개체를 Optional로 역직렬화한다. JsonMappingException이 발생하는 것을 볼 수 있다.

```java
@Test(expected = JsonMappingException.class)
public void givenFieldWithValue_whenDeserializing_thenThrowException
    String bookJson = "{ \"title\": \"Oliver Twist\", \"subTitle\": \"foo\" }";
    Book result = mapper.readValue(bookJson, Book.class);
}
```

스택 추적을 확인한다.

```text
com.fasterxml.jackson.databind.JsonMappingException:
  Can not construct instance of java.util.Optional:
  no String-argument constructor/factory method to deserialize from String value ('The Parish Boy's Progress')
```

기본적으로 Jackson에는 자막 값을 인수로 사용할 수 있는 생성자가 필요하다. Optional 필드에서는 그렇지 않다.

## 2. 솔루션
원하는 것은 Jackson이 빈 Optional을 null로 처리 하고 현재 Optional을 해당 값을 나타내는 필드로 처리하는 것이다.

이 문제는 해결되었다. Jackson에는 Optional을 포함하여 JDK 8 데이터 유형을 처리하는 모듈 세트가 있다.

### 1) Maven 종속성 및 등록
먼저 최신 버전을 Maven 종속성으로 추가한다.

```xml
<dependency>
   <groupId>com.fasterxml.jackson.datatype</groupId>
   <artifactId>jackson-datatype-jdk8</artifactId>
   <version>2.13.3</version>
</dependency>
```

이제 ObjectMapper에 모듈을 등록한다.

```json
ObjectMapper mapper = new ObjectMapper();
mapper.registerModule(new Jdk8Module());
```

### 2) 직렬화
이제 테스트 한다. Book 개체를 다시 직렬화하려고 하면 중첩된 JSON이 아닌 자막이 있는 것을 볼 수 있다.

```java
Book book = new Book();
book.setTitle("Oliver Twist");
book.setSubTitle(Optional.of("The Parish Boy's Progress"));
String serializedBook = mapper.writeValueAsString(book);
 
assertThat(from(serializedBook).getString("subTitle"))
  .isEqualTo("The Parish Boy's Progress");
```

빈 Book을 직렬화하려고 하면 null로 저장된다.

```java
book.setSubTitle(Optional.empty());
String serializedBook = mapper.writeValueAsString(book);
 
assertThat(from(serializedBook).getString("subTitle")).isNull();
```

### 3) 역직렬화
이제 역직렬화 테스트를 반복한다. Book을 다시 읽어보면 더 이상 JsonMappingException이 발생하지 않는다.

```java
Book newBook = mapper.readValue(result, Book.class);
 
assertThat(newBook.getSubTitle()).isEqualTo(Optional.of("The Parish Boy's Progress"));
```

마지막으로 이번에는 null을 사용하여 테스트를 다시 반복한다. JsonMappingException을 얻지 못하고 실제로 빈 Optional을 가지고 있음을 다시 한 번 보게 된다.

```java
assertThat(newBook.getSubTitle()).isEqualTo(Optional.empty());
```

## [출처 및 참고]
* [https://www.baeldung.com/jackson-optional](https://www.baeldung.com/jackson-optional)
