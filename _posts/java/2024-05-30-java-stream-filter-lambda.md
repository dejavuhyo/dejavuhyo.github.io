---
title: Java Stream Filter Lambda
author: dejavuhyo
date: 2024-05-30 14:22:00 +0900
categories: [Language, Java]
tags: [java-stream, stream-filter, filter-lambda, 자바-스트림, 스트림-필터, 필터-람다]
---

## 1. Stream.filter() 사용
`filter()` 메소드는 주어진 Predicate와 일치하는 스트림 요소를 필터링할 수 있는 Stream 인터페이스의 중간 작업이다.

```java
Stream<T> filter(Predicate<? super T> predicate)
```

어떻게 작동하는지 확인하기 위해 Customer 클래스를 만든다.

```java
public class Customer {
    private String name;
    private int points;
    //Constructor and standard getters
}
```

또한 customers 컬렉션을 만든다.

```java
Customer john = new Customer("John P.", 15);
Customer sarah = new Customer("Sarah M.", 200);
Customer charles = new Customer("Charles B.", 150);
Customer mary = new Customer("Mary T.", 1);

List<Customer> customers = Arrays.asList(john, sarah, charles, mary);
```

### 1) 컬렉션 필터링
`filter()` 메서드의 일반적인 사용 사례는 컬렉션을 처리하는 것이다.

포인트가 100점 이상인 고객 목록을 만든다. 이를 위해 람다 표현식을 사용할 수 있다.

```java
List<Customer> customersWithMoreThan100Points = customers
  .stream()
  .filter(c -> c.getPoints() > 100)
  .collect(Collectors.toList());
```

람다 표현식의 약어인 메소드 참조를 사용할 수도 있다.

```java
List<Customer> customersWithMoreThan100Points = customers
  .stream()
  .filter(Customer::hasOverHundredPoints)
  .collect(Collectors.toList());
```

이 경우 Customer 클래스에 hasOverHundredPoints 메서드를 추가했다.

```java
public boolean hasOverHundredPoints() {
    return this.points > 100;
}
```

두 경우 모두 동일한 결과를 얻는다.

```java
assertThat(customersWithMoreThan100Points).hasSize(2);
assertThat(customersWithMoreThan100Points).contains(sarah, charles);
```

### 2) 여러 기준으로 컬렉션 필터링
또한 `filter()`와 함께 여러 조건을 사용할 수 있다. 예를 들어 포인트와 이름으로 필터링할 수 있다.

```java
List<Customer> charlesWithMoreThan100Points = customers
  .stream()
  .filter(c -> c.getPoints() > 100 && c.getName().startsWith("Charles"))
  .collect(Collectors.toList());

assertThat(charlesWithMoreThan100Points).hasSize(1);
assertThat(charlesWithMoreThan100Points).contains(charles);
```

## 2. 예외 처리
지금까지 예외를 발생시키지 않는 조건자와 함께 필터를 사용해 왔다. 실제로 Java의 기능적 인터페이스는 확인된 예외나 확인되지 않은 예외를 선언하지 않는다.

람다 식에서 예외를 처리하는 몇 가지 다른 방법이다.

### 1) 사용자 정의 래퍼 사용
먼저 Customer에 profilePhotoUrl을 추가한다.

```java
private String profilePhotoUrl;
```

또한 프로필의 가용성을 확인하기 위해 간단한 `hasValidProfilePhoto()` 메서드를 추가한다.

```java
public boolean hasValidProfilePhoto() throws IOException {
    URL url = new URL(this.profilePhotoUrl);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    return connection.getResponseCode() == HttpURLConnection.HTTP_OK;
}
```

`hasValidProfilePhoto()` 메서드가 IOException을 발생시키는 것을 볼 수 있다. 이 방법으로 고객을 필터링하려고 하면 다음과 같다.

```java
List<Customer> customersWithValidProfilePhoto = customers
  .stream()
  .filter(Customer::hasValidProfilePhoto)
  .collect(Collectors.toList());
```

다음 오류가 표시된다.

```java
Incompatible thrown types java.io.IOException in functional expression
```

이를 처리하기 위해 사용할 수 있는 대안 중 하나는 `try-catch` 블록으로 래핑하는 것이다.

```java
List<Customer> customersWithValidProfilePhoto = customers
  .stream()
  .filter(c -> {
      try {
          return c.hasValidProfilePhoto();
      } catch (IOException e) {
          //handle exception
      }
      return false;
  })
  .collect(Collectors.toList());
```

조건자에서 예외를 발생시켜야 하는 경우 RuntimeException과 같은 확인되지 않은 예외로 래핑할 수 있다.

### 2) Throwing 함수 사용
ThrowingFunction 라이브러리를 사용할 수 있다.

ThrowingFunction은 Java 기능 인터페이스에서 확인된 예외를 처리할 수 있는 오픈 소스 라이브러리이다.

pom에 [throwing-function 종속성](https://mvnrepository.com/artifact/com.pivovarit/throwing-function/1.5.1)을 추가한다.

```java
<dependency>
    <groupId>com.pivovarit</groupId>
    <artifactId>throwing-function</artifactId>
    <version>1.5.1</version>
</dependency>
```

조건자의 예외를 처리하기 위해 이 라이브러리는 검사된 예외를 래핑하는 `unchecked()` 메서드가 있는 ThrowingPredicate 클래스를 제공한다.

실제로 살펴본다.

```java
List customersWithValidProfilePhoto = customers
  .stream()
  .filter(ThrowingPredicate.unchecked(Customer::hasValidProfilePhoto))
  .collect(Collectors.toList());
```

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-filter-lambda](https://www.baeldung.com/java-stream-filter-lambda)
