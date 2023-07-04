---
title: Apache Commons Collections CollectionUtils
author: dejavuhyo
date: 2023-07-04 09:35:00 +0900
categories: [Language, Java]
tags: [apache-commons, apache-commons-collectionutils, collectionutils, 아파치-커먼즈]
---

## 1. 메이븐 종속성
CollectionUtils를 사용하려면 다음 종속성을 추가한다.

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.1</version>
</dependency>
```

## 2. 설정
고객 및 주소 클래스를 추가한다.

```java
public class Customer {
    private Integer id;
    private String name;
    private Address address;

    // standard getters and setters
}

public class Address {
    private String locality;
    private String city;

    // standard getters and setters
}
```

또한 구현을 테스트할 준비가 된 다음 Customer 및 List 인스턴스를 편리하게 보관한다.

```java
Customer customer1 = new Customer(1, "Daniel", "locality1", "city1");
Customer customer2 = new Customer(2, "Fredrik", "locality2", "city2");
Customer customer3 = new Customer(3, "Kyle", "locality3", "city3");
Customer customer4 = new Customer(4, "Bob", "locality4", "city4");
Customer customer5 = new Customer(5, "Cat", "locality5", "city5");
Customer customer6 = new Customer(6, "John", "locality6", "city6");

List<Customer> list1 = Arrays.asList(customer1, customer2, customer3);
List<Customer> list2 = Arrays.asList(customer4, customer5, customer6);
List<Customer> list3 = Arrays.asList(customer1, customer2);

List<Customer> linkedList1 = new LinkedList<>(list1);
```

## 3. CollectionUtils
Apache Commons CollectionUtils 클래스에서 가장 많이 사용되는 메서드이다.

### 1) Null이 아닌 요소만 추가
CollectionUtils의 addIgnoreNull 메서드를 사용하여 제공된 컬렉션에 null이 아닌 요소만 추가할 수 있다.

이 메서드의 첫 번째 인수는 요소를 추가하려는 컬렉션이고 두 번째 인수는 추가하려는 요소이다.

```java
@Test
public void givenList_whenAddIgnoreNull_thenNoNullAdded() {
    CollectionUtils.addIgnoreNull(list1, null);

    assertFalse(list1.contains(null));
}
```

null 이 목록에 추가되지 않았는지 확인한다.

### 2) 조합 목록
collate 메소드를 사용하여 이미 정렬된 두 목록을 대조 할 수 있다. 이 메서드는 병합하려는 두 목록을 인수로 사용하고 단일 정렬 목록을 반환한다.

```java
@Test
public void givenTwoSortedLists_whenCollated_thenSorted() {
    List<Customer> sortedList = CollectionUtils.collate(list1, list2);

    assertEquals(6, sortedList.size());
    assertTrue(sortedList.get(0).getName().equals("Bob"));
    assertTrue(sortedList.get(2).getName().equals("Daniel"));
}
```

### 3) 개체 변형
transform 메서드를 사용하여 클래스 A의 객체를 클래스 B의 다른 객체로 변환할 수 있다. 이 메서드는 클래스 A의 객체 목록과 변환기를 인수로 사용한다.

이 작업의 결과는 클래스 B의 개체 목록이다.

```java
@Test
public void givenListOfCustomers_whenTransformed_thenListOfAddress() {
    Collection<Address> addressCol = CollectionUtils.collect(list1, new Transformer<Customer, Address>() {
        public Address transform(Customer customer) {
            return customer.getAddress();
        }
    });

    List<Address> addressList = new ArrayList<>(addressCol);
    assertTrue(addressList.size() == 3);
    assertTrue(addressList.get(0).getLocality().equals("locality1"));
}
```

### 4) 개체 필터링
필터를 사용하여 주어진 조건을 만족하지 않는 객체를 목록에서 제거할 수 있다. 이 메서드는 목록을 첫 번째 인수로 사용하고 술어를 두 번째 인수로 사용한다.

filterInverse 메서드는 그 반대이다. Predicate가 true를 반환 하면 목록에서 개체를 제거한다.

입력 목록이 수정된 경우, 즉 하나 이상의 개체가 목록에서 필터링된 경우 filter 및 filterInverse 둘 다 true를 반환한다.

```java
@Test
public void givenCustomerList_WhenFiltered_thenCorrectSize() {
    
    boolean isModified = CollectionUtils.filter(linkedList1, new Predicate<Customer>() {
        public boolean evaluate(Customer customer) {
            return Arrays.asList("Daniel","Kyle").contains(customer.getName());
        }
    });

    assertTrue(linkedList1.size() == 2);
}
```

boolean 플래그가 아닌 결과 목록을 반환하려는 경우 select 및 selectRejected를 사용할 수 있다.

### 5) 비어 있지 않은지 확
isNotEmpty 메소드는 목록에 최소한 하나의 요소가 있는지 확인하고 싶을 때 매우 편리하다. 동일한 것을 확인하는 다른 방법은 다음과 같다.

```java
boolean isNotEmpty = (list != null && list.size() > 0);
```

위의 코드 행은 동일하지만 `CollectionUtils.isNotEmpty`는 코드를 더 깔끔하게 유지한다.

```java
@Test
public void givenNonEmptyList_whenCheckedIsNotEmpty_thenTrue() {
    assertTrue(CollectionUtils.isNotEmpty(list1));
}
```

isEmpty는 그 반대이다. 주어진 목록이 null인지 또는 목록에 요소가 없는지 확인한다.

```java
List<Customer> emptyList = new ArrayList<>();
List<Customer> nullList = null;

assertTrue(CollectionUtils.isEmpty(nullList));
assertTrue(CollectionUtils.isEmpty(emptyList));
```

### 6) 포함 확인
isSubCollection을 사용하여 컬렉션이 다른 컬렉션에 포함되어 있는지 확인할 수 있다. isSubCollection은 두 개의 컬렉션을 인수로 사용하고 첫 번째 컬렉션이 두 번째 컬렉션의 하위 컬렉션인 경우 true를 반환한다.

```java
@Test
public void givenCustomerListAndASubcollection_whenChecked_thenTrue() {
    assertTrue(CollectionUtils.isSubCollection(list3, list1));
}
```

개체가 첫 번째 컬렉션에서 발생하는 횟수가 두 번째 컬렉션에서 발생하는 횟수보다 작거나 같은 경우 컬렉션은 다른 컬렉션의 하위 컬렉션이다.

### 7) 컬렉션의 교차점
`CollectionUtils.intersection` 메서드를 사용하여 두 컬렉션의 교차점을 얻을 수 있다. 이 메서드는 두 개의 컬렉션을 사용하고 두 입력 컬렉션에서 공통적인 요소 컬렉션을 반환한다.

```java
@Test
public void givenTwoLists_whenIntersected_thenCheckSize() {
    Collection<Customer> intersection = CollectionUtils.intersection(list1, list3);
    assertTrue(intersection.size() == 2);
}
```

결과 컬렉션에서 요소가 발생하는 횟수는 지정된 각 컬렉션에서 발생하는 횟수의 최소값이다.

### 8) 컬렉션 빼기
`CollectionUtils.subtract`는 두 개의 컬렉션을 입력으로 사용하고 첫 번째 컬렉션에는 있지만 두 번째 컬렉션에는 없는 요소를 포함하는 컬렉션을 반환한다.

```java
@Test
public void givenTwoLists_whenSubtracted_thenCheckElementNotPresentInA() {
    Collection<Customer> result = CollectionUtils.subtract(list1, list3);
    assertFalse(result.contains(customer1));
}
```

결과에서 수집이 발생하는 횟수는 첫 번째 수집에서 발생하는 횟수에서 두 번째 수집에서 발생하는 횟수를 뺀 것이다.

### 9) 컬렉션 연합
`CollectionUtils.union`은 두 컬렉션의 합집합을 수행하고 첫 번째 또는 두 번째 컬렉션에 있는 모든 요소를 ​​포함하는 컬렉션을 반환한다.

```java
@Test
public void givenTwoLists_whenUnioned_thenCheckElementPresentInResult() {
    Collection<Customer> union = CollectionUtils.union(list1, list2);
 
    assertTrue(union.contains(customer1));
    assertTrue(union.contains(customer4));
}
```

결과 컬렉션에서 요소가 발생하는 횟수는 지정된 각 컬렉션에서 발생하는 최대 횟수이다.

## [출처 및 참고]
* [https://www.baeldung.com/apache-commons-collection-utils](https://www.baeldung.com/apache-commons-collection-utils)
