---
title: Java Jackson @JsonFormat
author: dejavuhyo
date: 2024-01-01 15:26:00 +0900
categories: [Language, Java]
tags: [java-jackson, jackson-jsonformat, jsonformat]
---

## 1. 메이븐 의존성
`@JsonFormat`은 [jackson-databind](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind) 패키지에 정의되어 있으므로 다음 Maven 종속성이 필요하다.

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.13.3</version>
</dependency>
```

## 2. 시작하기

### 1) 기본 형식 사용
사용자를 나타내는 클래스와 함께 `@JsonFormat` 주석을 사용하는 개념을 보여주는 것부터 시작한다.

주석의 세부 사항을 설명하고 싶기 때문에 요청시 User 개체가 생성되고(데이터베이스에 저장되거나 로드되지 않음) JSON으로 직렬화된다.

```java
public class User {
    private String firstName;
    private String lastName;
    private Date createdDate = new Date();

    // standard constructor, setters and getters
}
```

이 코드 예제를 빌드하고 실행하면 다음 출력이 반환된다.

```json
{"firstName":"John","lastName":"Smith","createdDate":1482047026009}
```

보시다시피, createdDate 필드는 날짜 Date에 사용되는 기본 형식인 epoch 이후의 밀리초 수로 표시된다.

### 2) Getter에서 주석 사용
이제 `@JsonFormat`을 사용하여 CreateDate 필드를 직렬화하는 형식을 지정한다.

이 변경 사항에 대해 업데이트된 User 클래스를 확인한다. 날짜 형식을 지정하기 위해 표시된 대로 CreateDate 필드에 주석을 추가한다.

pattern argument에 사용되는 데이터 형식은 SimpleDateFormat에 의해 지정된다.

```java
@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd@HH:mm:ss.SSSZ")
private Date createdDate;
```

이 변경 사항을 적용하여 프로젝트를 다시 빌드하고 실행한다.

결과는 다음과 같다.

```json
{"firstName":"John","lastName":"Smith","createdDate":"2016-12-18@07:53:34.740+0000"}
```

여기서는 `@JsonFormat` 주석을 사용하여 지정된 SimpleDateFormat 형식을 사용하여 CreateDate 필드의 형식을 지정했다.

위의 예에서는 필드에 주석을 사용하는 방법이다. getter 메소드(속성)에서도 사용할 수 있다.

예를 들어, 호출 시 계산되는 속성이 있을 수 있다. 이러한 경우 getter 메소드에 주석을 사용할 수 있다.

또한 순간의 날짜 부분만 반환하도록 패턴을 변경했다.

```java
@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd")
public Date getCurrentDate() {
    return new Date();
}
```

결과는 다음과 같다.

```json
{ ... , "currentDate":"2016-12-18", ...}
```

### 3) Locale 지정
날짜 형식을 지정하는것 외에도 직렬화를 위한 로케일을 지정할 수도 있다.

이 매개변수를 지정하지 않으면 기본 로케일로 직렬화가 수행된다.

```java
@JsonFormat(
  shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd@HH:mm:ss.SSSZ", locale = "en_GB")
public Date getCurrentDate() {
    return new Date();
}
```

### 4) Shape 지정
모양이 `JsonFormat.Shape.NUMBER`로 설정된 `@JsonFormat`을 사용하면 Date 유형에 대한 기본 출력이 생성된다(epoch 이후의 밀리초 수).

매개변수 패턴은 이 경우에 적용되지 않으며 무시된다.

```java
@JsonFormat(shape = JsonFormat.Shape.NUMBER)
public Date getDateNum() {
    return new Date();
}
```

출력을 확인한다.

```json
{ ..., "dateNum":1482054723876 }
```

## 3. 대소문자를 구분하지 않는 역직렬화
때로는 다양한 소스로부터 JSON 문서를 수신하는데 속성 이름에서 동일한 대소문자 규칙을 따르지 않는 경우가 있다. 예를 들어, 하나의 JSON 문서에는 `"firstname": "John"`이 있지만 다른 문서에는 `"firstName": "John"` 또는 `"FIRSTNAME": "John"`이 포함될 수 있다.

기본 역직렬 변환기는 대소문자가 다른 속성 이름을 자동으로 인식할 수 없다.

먼저 입력으로 JSON 문서가 있다고 가정한다.

```java
static final String JSON_STRING = "{\"FIRSTNAME\":\"John\",\"lastname\":\"Smith\",\"cReAtEdDaTe\":\"2016-12-18@07:53:34.740+0000\"}";
```

보시다시피 "FIRSTNAME", "lastname" 및 "cReAtEdDaTe" 세 가지 속성은 완전히 다른 대소문자 규칙을 따른다. 이제 이 JSON 문서를 User 개체로 역직렬화하면 UnrecognizedPropertyException이 발생한다.

```java
assertThatThrownBy(() -> new ObjectMapper().readValue(JSON_STRING, User.class)).isInstanceOf(UnrecognizedPropertyException.class);
```

위의 테스트에서 볼 수 있듯이 Assertj의 예외 어설션을 사용하여 예상된 예외가 발생하는지 확인한다.

이런 종류의 문제를 해결하려면 deserializer가 대소문자를 구분하지 않는 deserialization을 수행하도록 해야 한다. 다음으로 `@JsonFormat` 주석을 사용하여 이를 달성하는 방법이다.

`@JsonFormat` 주석을 사용하면 with 속성 `@JsonFormat(with = JsonFormat.Feature … )`을 통해 `JsonFormat.Feature` 값 세트를 설정할 수 있다.

또한 `JsonFormat.Feature`는 enum이다. 속성 직렬화 또는 역직렬화 동작을 지정하기 위한 옵션 세트가 미리 정의되어 있다.

ACCEPT_CASE_INSENSITIVE_PROPERTIES 기능은 역직렬 변환기에 속성 이름을 대소문자를 구분하지 않고 일치시키도록 지시한다. 이 기능을 사용하려면 클래스 수준 `@JsonFormat` 주석에 포함하면 된다.

이 주석과 기능을 사용하여 UserIgnoreCase 클래스를 만든다.

```java
@JsonFormat(with = JsonFormat.Feature.ACCEPT_CASE_INSENSITIVE_PROPERTIES)
class UserIgnoreCase {
    private String firstName;
    private String lastName;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd@HH:mm:ss.SSSZ")
    private Date createdDate;

    // the rest is the same as the User class
    ...
};
```

이제 JSON 입력을 UserIgnoreCase로 역직렬화하면 예상대로 작동한다.

```java
UserIgnoreCase result = new ObjectMapper().readValue(JSON_STRING, UserIgnoreCase.class);
SimpleDateFormat fmt = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSSzz");
Date expectedDate = fmt.parse("2016-12-18T07:53:34.740+0000");
assertThat(result)
  .isNotNull()
  .returns("John", from(UserIgnoreCase::getFirstName))
  .returns("Smith", from(UserIgnoreCase::getLastName))
  .returns(expectedDate, from(UserIgnoreCase::getCreatedDate));
```

하나의 단일 주장에서 여러 속성을 주장하기 위해 Assertj의 `return()` 및 `from()`을 사용했다. 매우 편리하고 코드를 더 쉽게 읽을 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/jackson-jsonformat](https://www.baeldung.com/jackson-jsonformat)
