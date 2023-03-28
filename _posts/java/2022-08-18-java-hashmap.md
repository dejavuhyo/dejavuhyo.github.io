---
title: Java HashMap
author: dejavuhyo
date: 2022-08-18 10:50:00 +0900
categories: [Language, Java]
tags: [java-hashmap, java-map, hashmap, map, map-put, map-entryset, map-keyset, 자바-hashmap, 자바-map, hashmap-선언, hashmap-값-추가, hashmap-값-삭제, hashmap-값-출력]
---

## 1. HashMap
HashMap은 Map 인터페이스를 구현한 대표적인 Map 컬렉션이다. Map 인터페이스를 상속하고 있기에 Map의 성질을 그대로 가지고 있다.

Map은 키와 값으로 구성된 Entry객체를 저장하는 구조를 가지고 있는 자료구조이다. 여기서 키와 값은 모두 객체이다. 값은 중복 저장될 수 있지만 키는 중복 저장될 수 없다. 만약 기존에 저장된 키와 동일한 키로 값을 저장하면 기존의 값은 없어지고 새로운 값으로 대치된다.

HashMap은 이름 그대로 해싱(Hashing)을 사용하기 때문에 많은 양의 데이터를 검색하는데 있어서 뛰어난 성능을 보인다.

![java-hashmap](/assets/img/2022-08-18-java-hashmap/java-hashmap.png)

## 2. HashMap 선언
HashMap을 생성하려면 키 타입과 값 타입을 파라미터로 주고 기본생성자를 호출하면 된다.

HashMap은 저장공간보다 값이 추가로 들어오면 List처럼 저장공간을 추가로 늘리는데 List처럼 저장공간을 한 칸씩 늘리지 않고 약 두배로 늘린다. 여기서 과부하가 많이 발생한다.

그렇기에 초기에 저장할 데이터 개수를 알고 있다면 [Map의 초기 용량을 지정](https://d2.naver.com/helloworld/831311)해주는 것이 좋다.

```java
HashMap<String, String> map1 = new HashMap<>(String, String); // HashMap생성
HashMap<String, String> map2 = new HashMap<>(); // new에서 타입 파라미터 생략가능
HashMap<String, String> map3 = new HashMap<>(map1); // map1의 모든 값을 가진 HashMap생성
HashMap<String, String> map4 = new HashMap<>(10); // 초기 용량(capacity)지정
HashMap<String, String> map5 = new HashMap<>(10, 0.7f); // 초기 capacity, load factor 지정
HashMap<String, String> map6 = new HashMap<String, String>() {% raw %}{{ // 초기값 지정
put("a", "b");
}}{% endraw %};
```

## 3. HashMap 값 추가
HashMap에 값을 추가하려면 `put(key, value)` 메소드를 사용한다. 선언 시 HashMap에 설정해준 타입과 같은 타입의 Key와 Value값을 넣어야 하며, 만약 입력되는 키 값이 HashMap 내부에 존재한다면 기존의 값은 새로 입력되는 값으로 대치된다.

```java
HashMap<Integer,String> map = new HashMap<>();
map.put(1,"사과"); //값 추가
map.put(2,"바나나");
map.put(3,"포도");
```

## 4. HashMap 값 삭제
HashMap에 값을 제거하려면 `remove(key)` 메소드를 사용한다. 오직 키값으로만 Map의 요소를 삭제할 수 있다. 모든 값을 제거하려면 `clear()` 메소드를 사용한다.

```java
HashMap<Integer, String> map = new HashMap<>() {% raw %}{{
    put(1, "사과");
    put(2, "바나나");
    put(3, "포도");
}}{% endraw %};

map.remove(1); // key값 1 제거
map.clear(); // 모든 값 제거
```

## 5. HashMap 값 출력
HashMap을 출력하는 방법에는 다양한 방법이 있다. 그냥 print하게 되면 `{}`로 묶어 Map의 전체 key값, value가 출력된다.

특정 key값의 value를 가져오고싶다면 `get(key)`를 사용하면 되고 전체를 출력하려면 `entrySet()`이나 `keySet()`메소드를 활용하여 Map의 객체를 반환받은 후 출력하면 됩니다.

`entrySet()`은 key와 value 모두가 필요할 경우 사용하며 `keySet()`은 key 값만 필요할 경우 사용하는데 key값만 받아서 `get(key)`를 활용하여 value도 출력할 수 있다.

그러나 key값을 이용해서 value를 찾는 과정은 시간이 많이 소모되므로, 많은 양의 데이터를 가져와야 한다면 `entrySet()`이 좋다. (약 20% ~ 200% 성능 저하가 있음)

```java
HashMap<Integer, String> map = new HashMap<>() {% raw %}{{ // 초기값 지정
    put(1, "사과");
    put(2, "바나나");
    put(3, "포도");
}}{% endraw %};

System.out.println(map); // 전체 출력 : {1=사과, 2=바나나, 3=포도}
System.out.println(map.get(1)); //key값 1의 value얻기 : 사과

// entrySet()
for (Entry<Integer, String> entry : map.entrySet()) {
    System.out.println("[Key]:" + entry.getKey() + " [Value]:" + entry.getValue());
}
// [Key]:1 [Value]:사과
// [Key]:2 [Value]:바나나
// [Key]:3 [Value]:포도

// keySet()
for (Integer i : map.keySet()) { // 저장된 key값 확인
    System.out.println("[Key]:" + i + " [Value]:" + map.get(i));
}
// [Key]:1 [Value]:사과
// [Key]:2 [Value]:바나나
// [Key]:3 [Value]:포도
```

## 6. Iterator 사용
HashMap의 전체출력 시 반복문을 사용하지 않고 Iterator를 사용하여도 된다. iterator로 Map안의 전체 요소를 출력하는 방법은 위와 같다.

```java
HashMap<Integer, String> map = new HashMap<>() {% raw %}{{ // 초기값 지정
    put(1, "사과");
    put(2, "바나나");
    put(3, "포도");
}}{% endraw %};

// entrySet().iterator()
Iterator<Entry<Integer, String>> entries = map.entrySet().iterator();
while (entries.hasNext()) {
    Map.Entry<Integer, String> entry = entries.next();
    System.out.println("[Key]:" + entry.getKey() + " [Value]:" + entry.getValue());
}
// [Key]:1 [Value]:사과
// [Key]:2 [Value]:바나나
// [Key]:3 [Value]:포도

// keySet().iterator()
Iterator<Integer> keys = map.keySet().iterator();
while (keys.hasNext()) {
    int key = keys.next();
    System.out.println("[Key]:" + key + " [Value]:" + map.get(key));
}
// [Key]:1 [Value]:사과
// [Key]:2 [Value]:바나나
// [Key]:3 [Value]:포도
```

## [출처 및 참고]
* [https://coding-factory.tistory.com/556](https://coding-factory.tistory.com/556)
