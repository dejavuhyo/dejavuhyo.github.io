---
title: Java List에서 Element 찾기
author: dejavuhyo
date: 2023-02-27 07:40:00 +0900
categories: [Language, Java]
tags: [java-list-element, list-element, java-list, java-element, 자바-리스트, 자바-엘리먼트, 리스트-엘리먼트]
---

## 1. 설정
Customer POJO를 정의한다.

```java
public class Customer {

    private int id;
    private String name;
    
    // getters/setters, custom hashcode/equals
}
```

고객의 ArrayList이다.

```java
List<Customer> customers = new ArrayList<>();
customers.add(new Customer(1, "Jack"));
customers.add(new Customer(2, "James"));
customers.add(new Customer(3, "Kelly"));
```

Customer클래스에서 `hashCode` 및 `equals`를 재정의했다.

equals의 현재 구현에 따라 동일한 ID를 가진 두 개의 Customer 객체는 동일한 것으로 간주한다.

그 과정에서 이 고객 목록을 사용할 것이다.

## 2. 자바 API 사용

### 1) contains()
`List`는 `contains`라는 메서드가 있다.

```java
boolean contains(Object element)
```

이름에서 알 수 있듯이 이 메서드는 목록에 지정된 element가 포함되어 있으면 true를 반환하고 그렇지 않으면 false를 반환한다.

따라서 목록에 특정 항목이 있는지 확인해야 할 때 다음을 수행할 수 있다.

```java
Customer james = new Customer(2, "James");
if (customers.contains(james)) {
    // ...
}
```

### 2) indexOf()
`indexOf`는 element를 찾는 또 다른 유용한 방법이다.

```java
int indexOf(Object element)
```

이 메서드는 주어진 목록에서 지정된 element가 처음 나타나는 인덱스를 반환하거나 목록에 element가 포함되어 있지 않으면 `-1`을 반환한다.

따라서 논리적으로 이 메서드가 `-1` 이외의 값을 반환하면 목록에 element가 포함되어 있음을 알 수 있다.

```java
if(customers.indexOf(james) != -1) {
    // ...
}
```

**이 방법을 사용하는 주요 이점은 주어진 목록에서 지정된 element의 위치를 ​​알려줄 수 있다는 것이다.**

### 3) Basic Looping
목록을 반복하는 전통적인 방법은 Java의 루프 구조 중 하나를 사용하는 것이다. 각 반복에서 목록의 현재 항목을 찾고 있는 element와 비교하여 일치하는지 확인한다.

```java
public Customer findUsingEnhancedForLoop(
  String name, List<Customer> customers) {

    for (Customer customer : customers) {
        if (customer.getName().equals(name)) {
            return customer;
        }
    }
    return null;
}
```

여기서 이름은 주어진 고객 목록에서 검색하는 이름을 나타낸다. 이 메서 드는 이름이 일치하는 목록의 첫 번째 Customer 개체를 반환하거나 그러한 Customer가 없으면 null을 반환한다.

### 4) Iterator를 사용한 루핑
Iterator는 항목 목록을 순회할 수 있는 또 다른 방법이다.

```java
public Customer findUsingIterator(
  String name, List<Customer> customers) {
    Iterator<Customer> iterator = customers.iterator();
    while (iterator.hasNext()) {
        Customer customer = iterator.next();
        if (customer.getName().equals(name)) {
            return customer;
        }
    }
    return null;
}
```

### 5) Java 8 Stream API
Java 8부터는 Stream API를 사용하여 List에서 element를 찾을 수 있다.

주어진 목록에서 특정 기준과 일치하는 element를 찾으려면 다음을 수행한다.

* 목록에서 `stream()` 호출한다.

* 적절한 술어로 `filter()` 메서드를 호출한다.

* 해당 element가 존재하는 경우 옵션으로 래핑 된 필터 조건자와 일치하는 첫 번째 element를 반환하는 `findAny()` 구성을 호출한다.

```java
Customer james = customers.stream()
  .filter(customer -> "James".equals(customer.getName()))
  .findAny()
  .orElse(null);
```

편의를 위해 선택적이 비어 있는 경우 기본적으로 null을 사용하지만, 모든 시나리오에서 이것이 항상 최선의 선택은 아닐 수 있다.

## 3. Google Guava
Google Guava는 스트림으로 수행할 수 있는 것과 유사한 기능을 제공한다.

```java
Customer james = Iterables.tryFind(customers,
  new Predicate<Customer>() {
      public boolean apply(Customer customer) {
          return "James".equals(customer.getName());
      }
  }).orNull();
```

Stream API와 마찬가지로 선택적으로 null 대신 기본값을 반환하도록 선택할 수 있다.

```java
Customer james = Iterables.tryFind(customers,
  new Predicate<Customer>() {
      public boolean apply(Customer customer) {
          return "James".equals(customer.getName());
      }
  }).or(customers.get(0));
```

위의 코드는 일치하는 항목이 없으면 목록의 첫 번째 element를 선택한다.

또한 **목록이나 술어가 null인 경우 Guava가 NullPointerException을 던진다.**

## 4. Apache Commons
Apache Commons를 사용하여 거의 동일한 방식으로 요소를 찾을 수 있다.

```java
Customer james = IterableUtils.find(customers,
  new Predicate<Customer>() {
      public boolean evaluate(Customer customer) {
          return "James".equals(customer.getName());
      }
  });
```

그러나 몇 가지 중요한 차이점이 있다.

* Apache Commons는 null 목록을 전달하면 null을 반환한다.

* Guava의 tryFind와 같은 기본값 기능을 제공하지 않는다.

## [출처 및 참고]
* [https://www.baeldung.com/find-list-element-java](https://www.baeldung.com/find-list-element-java)
