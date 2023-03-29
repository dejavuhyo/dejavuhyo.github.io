---
title: Java HashMap
author: dejavuhyo
date: 2023-03-29 09:30:00 +0900
categories: [Language, Java]
tags: [java-hashmap, hashmap, hashmap-guide, 자바-해시맵, 해시맵, 해시맵-가이드]
---

## 1. 기본 사용법
HashMap에서 맵은 key-value 매핑이다. 즉, 모든 키가 정확히 하나의 값에 매핑되고 키를 사용하여 맵에서 해당 값을 검색할 수 있음을 의미한다.

단순히 값을 목록에 추가하면 안 되는 이유를 알 수 있다. HashMap이 필요한 단순한 이유는 성능이다. 목록에서 특정 요소를 찾으려면 시간 복잡도는 `O(n)`이고 목록을 정렬하면 예를 들어 이진 검색을 사용하여 `O(log n)`이 된다.

HashMap의 장점은 값을 삽입하고 검색하는 시간 복잡도가 평균 `O(1)`이라는 것이다.

### 1) 설정
전체에 사용할 간단한 클래스이다.

```java
public class Product {

    private String name;
    private String description;
    private List<String> tags;

    // standard getters/setters/constructors

    public Product addTagsOfOtherProduct(Product product) {
        this.tags.addAll(product.getTags());
        return this;
    }
}
```

### 2) Put
String 유형의 키와 Product 유형의 요소를 사용하여 HashMap을 만든다.

```java
Map<String, Product> productsByName = new HashMap<>();
```

HashMap에 Product를 추가한다.

```java
Product eBike = new Product("E-Bike", "A bike with a battery");
Product roadBike = new Product("Road bike", "A bike for competition");
productsByName.put(eBike.getName(), eBike);
productsByName.put(roadBike.getName(), roadBike);
```

### 3) Get
키로 맵에서 값을 검색할 수 있다.

```java
Product nextPurchase = productsByName.get("E-Bike");
assertEquals("A bike with a battery", nextPurchase.getDescription());
```

맵에 존재하지 않는 키 값을 찾으려고 하면 null 값을 얻게 된다.

```java
Product nextPurchase = productsByName.get("Car");
assertNull(nextPurchase);
```

그리고 동일한 키로 두 번째 값을 삽입하면 해당 키에 대해 마지막으로 삽입된 값만 가져온다.

```java
Product newEBike = new Product("E-Bike", "A bike with a better battery");
productsByName.put(newEBike.getName(), newEBike);
assertEquals("A bike with a better battery", productsByName.get("E-Bike").getDescription());
```

### 4) 키로 Null
HashMap은 null을 키로 가질 수 있다.

```java
Product defaultProduct = new Product("Chocolate", "At least buy chocolate");
productsByName.put(null, defaultProduct);

Product nextPurchase = productsByName.get(null);
assertEquals("At least buy chocolate", nextPurchase.getDescription());
```

### 5) 동일한 키를 가진 값
동일한 객체를 다른 키로 두 번 삽입할 수 있다.

```java
productsByName.put(defaultProduct.getName(), defaultProduct);
assertSame(productsByName.get(null), productsByName.get("Chocolate"));
```

### 6) 값 제거
HashMap에서 key-value 매핑을 제거할 수 있다.

```java
productsByName.remove("E-Bike");
assertNull(productsByName.get("E-Bike"));
```

### 7) 키 또는 값이 맵에 존재하는지 확인
맵에 키가 있는지 확인하려면 `containsKey()` 메서드를 사용할 수 있다.

```java
productsByName.containsKey("E-Bike");
```

또는 맵에 값이 있는지 확인하기 위해 `containsValue()` 메서드를 사용할 수 있다.

```java
productsByName.containsValue(eBike);
```

이 예제에서는 두 메서드 호출 모두 true를 반환한다. 매우 비슷해 보이지만 이 두 메서드 호출 간에는 중요한 성능 차이가 있다. 키가 존재하는지 확인하는 복잡성은 `O(1)` 이고 요소를 확인하는 복잡성은 `O(n)`이다. 맵의 모든 요소를 반복해야 하기 때문이다.

### 8) HashMap 반복
HashMap의 모든 key-value 쌍을 반복하는 세 가지 기본 방법이 있다.

모든 키 집합을 반복할 수 있다.

```java
for(String key : productsByName.keySet()) {
    Product product = productsByName.get(key);
}
```

또는 모든 항목 집합을 반복할 수 있다.

```java
for(Map.Entry<String, Product> entry : productsByName.entrySet()) {
    Product product =  entry.getValue();
    String key = entry.getKey();
    //do something with the key and value
}
```

마지막으로 모든 값을 반복할 수 있다.

```java
List<Product> products = new ArrayList<>(productsByName.values());
```

## 2. Key
HashMap의 키로 모든 클래스를 사용할 수 있다. 그러나 맵이 제대로 작동하려면 `equals()` 및 `hashCode()`에 대한 구현을 제공해야 한다. product를 키로, price를 값으로 하는 맵을 가정한다.

```java
HashMap<Product, Integer> priceByProduct = new HashMap<>();
priceByProduct.put(eBike, 900);
```

`equals()` 및 `hashCode()` 메서드를 구현한다.

```java
@Override
public boolean equals(Object o) {
    if (this == o) {
        return true;
    }
    if (o == null || getClass() != o.getClass()) {
        return false;
    }

    Product product = (Product) o;
    return Objects.equals(name, product.name) &&
      Objects.equals(description, product.description);
}

@Override
public int hashCode() {
    return Objects.hash(name, description);
}
```

**`hashCode()` 및 `equals()`는 맵에서 값으로만 사용되는 클래스가 아니라 맵 키로 사용하려는 클래스에 대해서만 재정의해야 한다.**

## 3. Java 8의 추가 메서드
Java 8은 HashMap에 몇 가지 기능적 스타일 메서드를 추가했다.

각 방법에 대해 두 가지 예를 살펴본다. 첫 번째 예는 새로운 방법을 사용하는 방법을 보여주고 두 번째 예는 이전 버전의 Java에서 동일한 작업을 수행하는 방법을 보여준다.

### 1) forEach()
`forEach()` 메소드는 맵의 모든 요소를 반복하는 기능적 스타일 방식이다.

```java
productsByName.forEach( (key, product) -> {
    System.out.println("Key: " + key + " Product:" + product.getDescription());
    //do something with the key and value
});
```

자바 8 이전:

```java
for(Map.Entry<String, Product> entry : productsByName.entrySet()) {
    Product product =  entry.getValue();
    String key = entry.getKey();
    //do something with the key and value
}
```

### 2) getOrDefault()
`getOrDefault()` 메서드를 사용하여 맵에서 값을 가져오거나 지정된 키에 대한 매핑이 없는 경우 기본 요소를 반환할 수 있다.

```java
Product chocolate = new Product("chocolate", "something sweet");
Product defaultProduct = productsByName.getOrDefault("horse carriage", chocolate); 
Product bike = productsByName.getOrDefault("E-Bike", chocolate);
```

자바 8 이전:

```java
Product bike2 = productsByName.containsKey("E-Bike") 
    ? productsByName.get("E-Bike") 
    : chocolate;
Product defaultProduct2 = productsByName.containsKey("horse carriage") 
    ? productsByName.get("horse carriage") 
    : chocolate;
```

### 3) putIfAbsent()
이 방법을 사용하면 새 매핑을 추가할 수 있지만 주어진 키에 대한 매핑이 아직 없는 경우에만 가능하다.

```java
productsByName.putIfAbsent("E-Bike", chocolate);
```

자바 8 이전:

```java
if(productsByName.containsKey("E-Bike")) {
    productsByName.put("E-Bike", chocolate);
}
```

### 4) merge()
`merge()`를 사용 하면 매핑이 존재하는 경우 주어진 키의 값을 수정하거나 그렇지 않은 경우 새 값을 추가할 수 있다.

```java
Product eBike2 = new Product("E-Bike", "A bike with a battery");
eBike2.getTags().add("sport");
productsByName.merge("E-Bike", eBike2, Product::addTagsOfOtherProduct);
```

자바 8 이전:

```java
if(productsByName.containsKey("E-Bike")) {
    productsByName.get("E-Bike").addTagsOfOtherProduct(eBike2);
} else {
    productsByName.put("E-Bike", eBike2);
}
```

### 5) compute()
`compute()` 메서드를 사용하여 주어진 키에 대한 값을 계산할 수 있다.

```java
productsByName.compute("E-Bike", (k,v) -> {
    if(v != null) {
        return v.addTagsOfOtherProduct(eBike2);
    } else {
        return eBike2;
    }
});
```

자바 8 이전:

```java
if(productsByName.containsKey("E-Bike")) {    
    productsByName.get("E-Bike").addTagsOfOtherProduct(eBike2); 
} else {
    productsByName.put("E-Bike", eBike2); 
}
```

`merge()` 및 `compute()` 메서드가 매우 유사하다. `compute()` 메서드는 리매핑을 위한 키와 `BiFunction` 이라는 두 가지 인수를 허용한다. 그리고 `merge()`는 키, 키가 아직 존재하지 않는 경우 맵에 추가할 기본값, 재매핑을 위한 `BiFunction`의 세 가지 매개변수를 허용한다.

## 3. HashMap 내부
HashMap에서 키로 요소를 검색할 수 있다. 한 가지 접근 방식은 목록을 사용하고, 모든 요소를 반복하고, 키가 일치하는 요소를 찾으면 반환하는 것이다. 이 접근 방식의 시간 및 공간 복잡성은 모두 `O(n)`이다.

HashMap을 사용하면 넣기 및 가져오기 작업에 대해 `O(1)`의 평균 시간 복잡도와 `O(n)`의 공간 복잡도를 달성할 수 있다.

### 1) 해시 코드와 같음
모든 요소를 반복하는 대신 HashMap은 키를 기반으로 값의 위치를 계산하려고 시도한다.

단순한 접근 방식은 가능한 키만큼 많은 요소를 포함할 수 있는 목록을 갖는 것이다. 예를 들어 키가 소문자라고 가정한다. 그러면 크기가 26인 목록이 있으면 충분하고 키 'c'로 요소에 액세스하려는 경우 위치 3에 있는 요소임을 알 수 있으므로 직접 검색할 수 있다.

그러나 이 접근 방식은 훨씬 더 큰 keyspace가 있는 경우 그다지 효과적이지 않다. 예를 들어 키가 정수라고 가정한다. 이 경우 목록의 크기는 `2,147,483,647`이어야 한다. 대부분의 경우 요소 수가 훨씬 적기 때문에 할당된 메모리의 상당 부분이 사용되지 않은 상태로 남게 된다.

**HashMap은 이른바 버킷에 요소를 저장하고 버킷의 수를 용량 이라고 한다.**

맵에 값을 입력하면 키의 `hashCode()` 메서드를 사용하여 값을 저장할 버킷을 결정한다.

값을 검색하기 위해 HashMap은 `hashCode()`를 사용하여 동일한 방식으로 버킷을 계산한다. 그런 다음 해당 버킷에서 찾은 개체를 반복하고 키의 `equals()` 메서드를 사용하여 정확히 일치하는 항목을 찾는다.

### 2) 키의 불변성
대부분의 경우 변경 불가능한 키를 사용해야 한다. 또는 적어도 가변 키 사용의 결과를 알고 있어야 한다.

키를 맵에 value를 저장하는데 사용한 후 키가 변경되면 어떤 일이 발생하는지 살펴본다.

이 예에서는 MutableKey를 생성한다.

```java
public class MutableKey {
    private String name;

    // standard constructor, getter and setter

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        MutableKey that = (MutableKey) o;
        return Objects.equals(name, that.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name);
    }
}
```

테스트는 다음과 같다.

```java
MutableKey key = new MutableKey("initial");

Map<MutableKey, String> items = new HashMap<>();
items.put(key, "success");

key.setName("changed");

assertNull(items.get(key)); 
```

키가 변경되면 해당 값을 더 이상 가져올 수 없으며 대신 null이 반환된다. 이는 HashMap이 잘못된 버킷에서 검색하기 때문이다.

### 3) 충돌
올바르게 작동하려면 동일한 키가 동일한 해시를 가져야 하지만 다른 키가 동일한 해시를 가질 수 있다. 두 개의 다른 키가 동일한 해시를 가진 경우 해당 키에 속하는 두 값은 동일한 버킷에 저장된다. 버킷 내에서 값은 목록에 저장되고 모든 요소를 반복하여 검색된다. 이 비용은 `O(n)`이다.

Java 8([JEP 180](https://openjdk.org/jeps/180) 참조)부터 하나의 버킷 내부의 값이 저장되는 데이터 구조는 버킷에 8개 이상의 값이 포함된 경우 목록에서 균형 트리로 변경되고 다음과 같은 경우 다시 목록으로 변경된다. 어떤 시점에서는 버킷에 6개의 값만 남는다. 이렇게 하면 성능이 `O(log n)`으로 향상된다.

### 4) 용량 및 부하율
값이 여러 개인 버킷이 많은 것을 방지하기 위해 버킷의 `75%`(부하 계수)가 비어 있지 않으면 용량이 두 배가 된다. 부하율의 기본값은 `75%`이고 기본 초기 용량은 16이다. 둘 다 생성자에서 설정할 수 있다.

### 5) put 및 get 작업 요약
put 및 get 작업이 작동하는 방식을 요약한다.

**맵에 요소를 추가하면** HashMap이 버킷을 계산한다. 버킷에 이미 값이 포함되어 있으면 해당 버킷에 속한 목록(또는 트리)에 값이 추가된다. 부하율이 맵의 최대 부하율보다 커지면 용량이 2배가 된다.

**맵에서 값을 얻고자 할 때** HashMap은 버킷을 계산하고 목록(또는 트리)에서 동일한 키로 값을 가져온다.

## [출처 및 참고]
* [https://www.baeldung.com/java-hashmap](https://www.baeldung.com/java-hashmap)
