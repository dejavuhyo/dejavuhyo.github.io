---
title: Java 클린 코드
author: dejavuhyo
date: 2022-11-04 10:20:00 +0900
categories: [Language, Java]
tags: [java-clean-code, clean-code,  naming-convention, whitespaces, indentation, code-comments, 자바-클린-코드, 클린-코드, 명명-규칙, 공백, 들여-쓰기, 매개변수, 코드-주석]
---

## 1. 클린 코드란
클린 코드는 솔직히 이것에 대한 좋은 대답은 없다. 프로그래밍에서 일부 우려 사항은 일반 원칙에 도달하여 결과적으로 발생한다. 그러나 모든 프로그래밍 언어와 패러다임은 고유한 뉘앙스를 나타내므로 적절한 방식을 채택해야 한다.

넓은 의미에서 클린 코드는 모든 개발자가 쉽게 읽고 변경할 수 있는 코드로 요약할 수 있다. 이것은 개념을 지나치게 단순화한 것처럼 들릴 수 있다.

클린 코드에 대해 듣게 되면 어디에서나 Martin Fowler에 대한 언급을 접하게 될 것이다. 다음은 그가 한 곳에서 깨끗한 코드를 설명하는 방법이다.

> 어떤 바보라도 컴퓨터가 이해할 수 있는 코드를 작성할 수 있습니다. 좋은 프로그래머는 인간이 이해할 수 있는 코드를 작성합니다.

## 2. 클린 코드에 관심을 가져야 하는 이유
깨끗한 코드를 작성하는 것은 기술의 문제인 동시에 개인의 습관의 문제이다. 개발자로서 시간이 지남에 따라 경험과 지식을 통해 성장한다. 그러나 결국 깨끗한 코드를 개발하는 데 투자해야 하는 이유를 물어야 한다. 다른 사람들이 코드를 더 쉽게 읽을 수 있다는 것을 알지만, 그 동기가 충분한지 의문을 가진다.

클린 코딩 원칙은 우리가 생산하려는 소프트웨어와 관련된 많은 바람직한 목표를 달성하는 데 도움이 된다. 더 잘 이해하기 위해 살펴본다.

* 유지 관리 가능한 코드베이스: 우리가 개발하는 모든 소프트웨어는 생산적인 수명을 가지며 이 기간에 변경 및 일반적인 유지 관리가 필요하다. 깨끗한 코드는 시간이 지남에 따라 변경하고 유지 관리하기 쉬운 소프트웨어를 개발하는 데 도움이 될 수 있다.

* 더 쉬운 문제 해결: 소프트웨어는 다양한 내부 또는 외부 요인으로 인해 의도하지 않은 동작을 나타낼 수 있다. 수정 및 가용성 측면에서 빠른 처리가 필요한 경우가 많다. 클린 코딩 원칙으로 개발된 소프트웨어는 문제를 해결하기가 더 쉽다.

* 더 빠른 온보딩: 소프트웨어는 수명 기간 많은 개발자가 소프트웨어를 생성, 업데이트 및 유지 관리하는 것을 볼 수 있으며 개발자는 다른 시점에 합류한다. 이를 위해서는 높은 생산성을 유지하기 위해 더 빠른 온보딩이 필요하며 깨끗한 코드는 이 목표를 달성하는 데 도움이 된다.

## 3. 클린코드의 특징
클린 코딩 원칙으로 작성된 코드 베이스는 이들을 구별하는 몇 가지 특성을 나타낸다. 이러한 특성 중 일부를 살펴본다.

* 집중: 특정 문제를 해결하기 위해 코드를 작성해야 한다. 주어진 문제를 해결하는 것과 엄밀히 관련이 없는 일을 해서는 안 된다. 이것은 메서드, 클래스, 패키지 또는 모듈과 같은 코드 베이스의 모든 추상화 수준에 적용된다.

* 단순함: 이것은 클린 코드의 가장 중요하고 종종 무시되는 특성이다. 소프트웨어 설계 및 구현은 가능한 한 단순해야 원하는 결과를 달성하는 데 도움이 된다. 코드 베이스의 복잡성이 증가하면 오류가 발생하기 쉽고 읽기 및 유지 관리가 어려워진다.

* 테스트 가능: 클린 코드는 단순하면서도 당면한 문제를 해결해야 한다. 되도록 자동화된 방식으로 코드 베이스를 테스트하기 쉽고 직관적이어야 한다. 이것은 코드 베이스의 기준 동작을 설정하는 데 도움이 되며 아무것도 손상하지 않고 쉽게 변경할 수 있다.

나중에 리팩터링하는 것과 비교하여 이러한 특성을 염두에 두고 개발을 시작하는 것이 좋다. 이는 소프트웨어 수명 주기의 총소유 비용을 낮추는 결과를 가져온다.

## 4. 자바의 클린 코딩
Java에서 깨끗한 코딩 원칙을 통합하는 방법을 살펴본다. Java는 깨끗한 코드를 작성하는 데 도움이 되는 많은 모범 사례를 제공한다. 그것들을 다른 버킷으로 분류하고 코드 샘플로 깨끗한 코드를 작성하는 방법을 이해한다.

### 1) 프로젝트 구조
Java는 프로젝트 구조를 강제하지 않지만 소스 파일, 테스트, 구성, 데이터 및 기타 코드 아티팩트를 구성하기 위해 일관된 패턴을 따르는 것이 항상 유용하다. Java 용으로 널리 사용되는 빌드 도구인 Maven은 특정 프로젝트 구조를 규정한다. Maven을 사용하지 않을 수도 있지만 규칙을 따르는 것은 항상 좋다.

Maven이 생성하도록 제안한 폴더 중 일부이다.

* `src/main/java`: 소스 파일용

* `src/main/resources`: 속성과 같은 리소스 파일용

* `src/test/java`: 테스트 소스 파일용

* `src/test/resources`: 속성과 같은 테스트 리소스 파일용

이와 유사하게 Java 용으로 제안된 Bazel과 같은 다른 인기 있는 프로젝트 구조가 있으며 우리는 필요와 대상에 따라 하나를 선택해야 한다.

### 2) 명명 규칙
명명 규칙을 따르면 코드를 읽기 쉽게 만들고 유지 관리할 수 있다. Spring의 창시자인 Rod Johnson은 Spring에서 명명 규칙의 중요성을 강조한다.

> "무슨 일을 하는지 안다면 Spring 클래스나 인터페이스의 이름을 추측할 좋은 기회가 있습니다."

Java는 Java에서 이름을 지정할 때 준수해야 할 일련의 규칙을 규정한다. 잘 구성된 이름은 코드를 읽는 데 도움이 될 뿐만 아니라 코드의 의도에 대해 많은 정보를 전달한다. 몇 가지 예이다.

* 클래스: 객체 지향 개념의 클래스는 종종 실제 객체를 나타내는 객체에 대한 청사진이다. 따라서 명사를 사용하여 충분히 설명하는 클래스의 이름을 지정하는 것은 의미가 있다.

```java
public class Customer {
}
```

* 변수: Java의 변수는 클래스에서 생성된 객체의 상태를 캡처한다. 변수 이름은 변수의 의도를 명확하게 설명해야 한다.

```java
public class Customer {
    private String customerName;
}
```

* 메소드: Java의 메소드는 항상 클래스의 일부이므로 일반적으로 클래스에서 생성된 객체의 상태에 대한 작업을 나타낸다. 따라서 동사를 사용하여 메서드의 이름을 지정하는 것이 유용하다.

```java
public class Customer {
    private String customerName;
    public String getCustomerName() {
        return this.customerName;
    }
}
```

Java에서 식별자의 이름을 지정하는 방법에 대해서만 논의했지만 가독성을 위해 관찰해야 하는 camel 대소문자 구분과 같은 추가 모범 사례가 있다. 인터페이스, 열거형, 상수 이름 지정과 관련된 더 많은 규칙이 있을 수 있다.

### 3) 소스 파일 구조
소스 파일에는 다른 요소가 포함될 수 있다. Java 컴파일러는 일부 구조를 적용하지만, 대부분은 유동적이다. 그러나 소스 파일에 요소를 배치하는 특정 순서를 준수하면 코드 가독성이 크게 향상될 수 있다. Google에서 제공한 것과 Spring에서 제공한 것과 같이 영감을 얻을 수 있는 여러 인기 있는 스타일 가이드가 있다.

소스 파일에서 요소의 일반적인 순서는 다음과 같이 표시되어야 한다.

* 패키지 명세서
* 수입 명세서
  - 모든 정적 가져오기
  - 모든 비정적 가져오기
* 정확히 하나의 최상위 클래
  - 클래스 변수
  - 인스턴스 변수
  - 생성자
  - 행동 양식

위 외에도 메서드는 기능이나 범위에 따라 그룹화할 수 있다. 하나의 좋은 관례는 없으며, 아이디어는 한번 결정하고 일관되게 따라야 한다.

잘 구성된 소스 파일이다.

```java
# /src/main/java/com/baeldung/application/entity/Customer.java
package com.baeldung.application.entity;

import java.util.Date;

public class Customer {
    private String customerName;
    private Date joiningDate;
    public Customer(String customerName) {
        this.customerName = customerName;
        this.joiningDate = new Date();
    }

    public String getCustomerName() { 
        return this.customerName; 
    }

    public Date getJoiningDate() {
        return this.joiningDate;
    }
}
```

### 4) 공백
큰 텍스트 블록에 비해 짧은 단락을 읽고 이해하는 것이 더 쉽다는 것을 알고 있다. 코드를 읽을 때도 크게 다르지 않다. 잘 배치되고 일관된 공백과 빈 줄은 코드의 가독성을 향상할 수 있다.

여기에서 아이디어는 코드를 읽는 동안 사고 과정을 구성하는 데 도움이 될 수 있는 논리적 그룹을 코드에 도입하는 것이다. 여기에서 채택할 단일 규칙은 없지만, 일반적인 지침 세트와 가독성을 그 중심에 두려는 고유한 의도가 있다.

* 정적 블록, 필드, 생성자 및 내부 클래스를 시작하기 전에 두 개의 빈 줄

* 여러 줄로 된 메서드 서명 뒤의 빈 줄 하나

* if, for, catch와 같은 예약 키워드를 여는 괄호에서 분리하는 단일 공백

* else와 같이 예약된 키워드를 구분하는 단일 공백, 닫는 괄호에서 catch

여기에 있는 목록이 완전하지는 않지만 앞으로 나아가야 할 방향을 제시해야 한다.

### 5) 들여 쓰기
사소하지만 거의 모든 개발자는 잘 들여쓰기 된 코드가 훨씬 더 읽기 쉽고 이해하기 쉽다는 사실을 보증할 것이다. Java에는 코드 들여쓰기에 대한 단일 규칙이 없다. 여기서 핵심은 인기 있는 규칙을 채택하거나 비공개 규칙을 정의한 다음 조직 전체에서 일관되게 따르는 것이다.

몇 가지 중요한 들여쓰기 기준이다.

* 일반적인 모범 사례는 들여쓰기 단위인 4개의 공백을 사용하는 것이다. 일부 지침에서는 공백 대신 탭을 제안한다. 여기에 절대적인 모범 사례는 없지만, 핵심은 일관성을 유지하는 것이다.

* 일반적으로 라인 길이에 캡이 있어야 하지만 오늘날 개발자가 사용하는 더 큰 화면으로 인해 기존 80보다 높게 설정할 수 있다.

* 마지막으로 많은 표현 식이 한 줄에 맞지 않기 때문에 일관되게 끊어야 한다.
 - 쉼표 뒤의 Break 메서드 호출
 - 연산자 앞에 표현식 나누기
 - 더 나은 가독성을 위해 줄 바꿈 된 줄 들여쓰기

예이다.

```java
List<String> customerIds = customer.stream()
  .map(customer -> customer.getCustomerId())
  .collect(Collectors.toCollection(ArrayList::new));
```

### 6) 메소드 매개변수
매개변수는 메서드가 사양에 따라 작동하는 데 필수적이다. 그러나 매개변수의 긴 목록은 누군가가 코드를 읽고 이해하는 것을 어렵게 만들 수 있다.

* 메소드가 허용하는 매개변수의 수를 제한한다. 세 개의 매개변수가 하나의 좋은 선택이 될 수 있다.

* 권장 매개변수보다 더 많은 매개변수가 필요한 경우 메소드 리팩토링을 고려한다. 일반적으로 긴 매개변수 목록은 메소드가 여러 작업을 수행할 수 있음을 나타낸다.

* 매개변수를 사용자 정의 유형으로 묶는 것을 고려할 수 있지만 관련 없는 매개변수를 단일 유형으로 덤프하지 않도록 주의해야 한다.

* 마지막으로 이 제안을 사용하여 코드의 가독성을 판단해야 하지만 그것에 대해 현학적이어서는 안 된다.

이에 대한 예이다.

```java
public boolean setCustomerAddress(String firstName, String lastName, String streetAddress, 
  String city, String zipCode, String state, String country, String phoneNumber) {
}

// This can be refactored as below to increase readability

public boolean setCustomerAddress(Address address) {
}
```

### 7) 하드코딩
코드의 값을 하드코딩하면 종종 여러 부작용이 발생할 수 있다. 예를 들어 중복이 발생하여 변경이 더 어려워질 수 있다. 값이 동적이어야 하는 경우 종종 바람직하지 않은 동작으로 이어질 수 있다. 대부분의 경우 하드코딩된 값은 다음 방법의 하나로 리팩토링할 수 있다.

* Java 내에서 정의된 상수 또는 열거형으로 대체 고려

* 또는 클래스 수준 또는 별도의 클래스 파일에 정의된 상수로 대체

* 가능한 경우 구성 또는 환경에서 선택할 수 있는 값으로 대체

예이다.

```java
private int storeClosureDay = 7;

// This can be refactored to use a constant from Java

private int storeClosureDay = DayOfWeek.SUNDAY.getValue()
```

이에 대한 엄격한 지침은 없다. 그러나 일부 사람들이 나중에 이 코드를 읽고 유지해야 할 필요가 있다는 사실을 인식해야 한다. 적합하고 일관된 규칙을 선택해야 한다.

### 8) 코드 주석
코드 주석은 중요하지 않은 측면을 이해하기 위해 코드를 읽는 동안 유용할 수 있다. 동시에 주석에 명백한 내용이 포함되지 않도록 주의해야 한다. 이것은 주석을 부풀려 관련 부분을 읽기 어렵게 만들 수 있다.

Java는 구현 주석과 문서 주석의 두 가지 유형의 주석을 허용한다. 그들은 다른 목적과 다른 형식을 가지고 있다.

* 문서/JavaDoc 주석
  - 여기의 청중은 코드베이스의 사용자이다.
  - 여기에 있는 세부 정보는 일반적으로 구현이 필요 없으며 사양에 더 중점을 둔다.
  - 일반적으로 코드베이스와 독립적으로 유용하다.

* 구현/차단 주석
  - 여기 청중은 코드베이스에서 작업하는 개발자이다.
  - 여기에 있는 세부 정보는 구현에 따라 다르다.
  - 일반적으로 코드베이스와 함께 유용하다.

그렇다면 유용하고 상황에 맞게 최적화하는 방법이다.

* 주석은 코드를 보완해야 한다. 주석 없이 코드를 이해할 수 없는 경우 리팩토링해야 할 수도 있다.

* 사소한 디자인 결정을 설명하기 위해 블록 주석을 거의 사용하지 않아야 한다.

* 대부분의 클래스, 인터페이스, 공개 및 보호 메소드에 대해 JavaDoc 주석을 사용해야 한다.

* 모든 주석은 가독성을 위해 적절한 들여쓰기로 잘 구성되어야 한다.

의미 있는 문서 주석의 예이다.

```java
/**
* This method is intended to add a new address for the customer.
* However do note that it only allows a single address per zip
* code. Hence, this will override any previous address with the
* same postal code.
*
* @param address an address to be added for an existing customer
*/
/*
* This method makes use of the custom implementation of equals 
* method to avoid duplication of an address with same zip code.
*/
public addCustomerAddress(Address address) {
}
```

### 9) 로깅
디버깅을 위해 프로덕션 코드에 손을 댄 적이 있는 사람은 어느 시점에서 더 많은 로그를 갈망했다. 일반적으로 개발 및 특히 유지 관리에서 로그의 중요성은 아무리 강조해도 지나치지 않다.

Java에는 SLF4J, Logback을 포함하여 로깅을 위한 많은 라이브러리와 프레임워크가 있다. 코드베이스에서 로깅을 매우 간단하게 만들지만, 로깅 모범 사례에 주의를 기울여야 한다. 그렇지 않으면 로깅이 도움이 되지 않고 유지 관리의 악몽이 될 수 있다. 다음 모범 사례 중 일부이다.

* 과도한 로깅을 피하고 문제 해결에 도움이 될 수 있는 정보를 생각한다.

* 로그 수준을 현명하게 선택한다. 프로덕션에서 선택적으로 로그 수준을 활성화할 수 있다.

* 로그 메시지의 컨텍스트 데이터를 사용하여 매우 명확하고 설명적이어야 한다.

* 더 빠른 분석을 위해 로그 메시지 추적, 집계, 필터링에 외부 도구 사용한다.

올바른 수준의 설명적 로깅 예이다.

```java
logger.info(String.format("A new customer has been created with customer Id: %s", id));
```

## 5. 추가 사항
코드 형식 지정 규칙을 강조 표시했지만, 주의해야 하는 유일한 규칙은 아니다. 읽을 수 있고 유지 관리할 수 있는 코드는 시간이 지남에 따라 축적된 수많은 추가 모범 사례의 이점을 얻을 수 있다.

시간이 지남에 따라 재미있는 두 문자 어를 접했을 수 있다. 그것들은 본질적으로 더 나은 코드를 작성하는 데 도움이 될 수 있는 단일 또는 일련의 원칙으로 학습을 포착한다. 그러나 존재한다는 이유만으로 따라 해서는 안 된다는 점에 유의한다. 대부분의 경우 이들이 제공하는 이점은 코드베이스의 크기와 복잡성에 비례한다. 원칙을 채택하기 전에 코드베이스에 액세스해야 한다. 더 중요한 것은 우리가 그들과 일관성을 유지해야 한다는 것이다.

### 1) SOLID
SOLID는 이해할 수 있고 유지 관리할 수 있는 소프트웨어를 작성하기 위해 제시한 5가지 원칙에서 파생된 약어이다.

* Single Responsibility Principle: 정의하는 모든 인터페이스, 클래스 또는 메소드에는 명확하게 정의된 목표가 있어야 한다. 본질적으로 이상적으로는 한 가지를 잘 수행해야 한다. 이것은 효과적으로 테스트할 수 있는 더 작은 메소드와 클래스로 이어진다.

* Open-Closed Principle: 우리가 작성하는 코드는 이상적으로는 확장을 위해 개방되어야 하지만 수정을 위해 폐쇄되어야 한다. 이것이 효과적으로 의미하는 바는 클래스를 수정할 필요가 없는 방식으로 작성되어야 한다는 것이다. 그러나 상속이나 구성을 통한 변경을 허용해야 한다.

* Liskov Substitution Principle: 이 원칙은 모든 하위 클래스 또는 파생 클래스가 부모 또는 기본 클래스를 대체할 수 있어야 한다고 명시하고 있다. 이것은 코드베이스의 결합을 줄이는 데 도움이 되며, 따라서 재사용성을 향상한다.

* Interface Segregation Principle: 인터페이스를 구현하는 것은 클래스에 특정 동작을 제공하는 방법이다. 그러나 클래스는 필요하지 않은 메서드를 구현할 필요가 없다. 이것이 우리에게 요구하는 것은 더 작고 더 집중된 인터페이스를 정의하는 것이다.

* Dependency Inversion Principle: 이 원칙에 따르면 클래스는 구체적인 구현이 아닌 추상화에만 의존해야 한다. 이는 클래스가 종속성에 대한 인스턴스 생성을 책임지지 않아야 함을 의미한다. 오히려 그러한 종속성은 클래스에 주입되어야 한다.

### 2) DRY & KISS
DRY는 "Don's Repeat Yourself"의 약자이다. 이 원칙은 코드 조각이 소프트웨어 전체에서 반복되어서는 안 된다는 것이다. 이 원칙의 근거는 중복을 줄이고 재사용성을 높이는 것이다. 그러나 이것을 너무 문자 그대로 받아들이는 데 주의해야 한다. 일부 중복은 실제로 코드 가독성과 유지 관리성을 향상할 수 있다.

KISS는 "Keep It Simple, Stupid"의 약자이다. 이 원칙은 코드를 가능한 한 단순하게 유지하도록 노력해야 한다는 것이다. 이렇게 하면 시간이 지남에 따라 쉽게 이해하고 유지 관리할 수 있다. 앞서 언급한 몇 가지 원칙에 따라 클래스와 메서드에 초점을 맞추고 작게 유지하면 코드가 더 간단해진다.

### 3) TDD
TDD는 "Test Driven Development"의 약자이다. 이것은 자동화된 테스트가 실패한 경우에만 코드를 작성하도록 요청하는 프로그래밍 방식이다. 따라서 자동화된 테스트의 설계 개발부터 시작해야 한다. Java에는 JUnit 및 TestNG와 같은 자동화된 단위 테스트를 작성하기 위한 여러 프레임워크가 있다.

그러한 연습의 이점은 엄청나다. 이것은 항상 예상대로 작동하는 소프트웨어로 이어진다. 항상 테스트를 시작할 때 작업 코드를 작은 덩어리로 점진적으로 추가한다. 또한 새 테스트 또는 이전 테스트가 실패할 경우에만 코드를 추가한다. 이는 재사용성으로도 이어진다는 것을 의미한다.

## 6. 도움말 도구
깨끗한 코드를 작성하는 것은 원칙과 관행의 문제가 아니라 개인의 습관이다. 배우고 적응하면서 더 나은 개발자로 성장하는 경향이 있다. 그러나 대규모 팀에서 일관성을 유지하기 위해 일부 시행도 연습해야 한다.

코드 리뷰는 일관성을 유지하고 건설적인 피드백을 통해 개발자가 성장하는 데 도움이 되는 훌륭한 도구이다.

그러나 코드 검토 중에 이러한 모든 원칙과 모범 사례를 수동으로 검증할 필요는 없다. Java OffHeap의 Freddy Guime은 일부 품질 검사를 자동화하여 항상 코드 품질의 특정 임계값에 도달하는 것의 가치에 관해 이야기한다.

Java 생태계에서 사용할 수 있는 몇 가지 도구가 있다. 이 도구는 코드 검토자의 이러한 책임 중 적어도 일부를 제거한다. 이러한 도구 중 일부가 무엇인지 살펴본다.

* 코드 포맷터: Eclipse 및 IntelliJ를 포함한 대부분의 인기 있는 Java 코드 편집기는 자동 코드 포맷을 허용한다. 기본 형식 지정 규칙을 사용하거나 사용자 지정하거나 사용자 지정 형식 지정 규칙으로 바꿀 수 있다. 이것은 많은 구조적 코드 규칙을 처리한다.

* 정적 분석 도구: SonarQube, Checkstyle, PMD 및 SpotBugs를 포함하여 Java용 정적 코드 분석 도구가 몇 가지 있다. 그대로 사용하거나 특정 프로젝트에 맞게 사용자 지정할 수 있는 풍부한 규칙 집합이 있다. 명명 규칙 위반 및 리소스 누출과 같은 많은 코드 냄새를 감지하는 데 탁월하다.

## [출처 및 참고]
* <https://www.baeldung.com/java-clean-code>