---
title: Java 두 개의 맵 병합
author: dejavuhyo
date: 2023-04-25 09:10:00 +0900
categories: [Language, Java]
tags: [java-merge-maps, merge-maps, 자바-맵-병합, 맵-병합]
---

## 1. 초기화
두 개의 Map 인스턴스를 정의한다.

```java
private static Map<String, Employee> map1 = new HashMap<>();
private static Map<String, Employee> map2 = new HashMap<>();
```

Employee 클래스는 다음과 같다.

```java
public class Employee {

    private Long id;
    private String name;

    // constructor, getters, setters
}
```

그런 다음 일부 데이터를 Map 인스턴스에 푸시할 수 있다.

```java
Employee employee1 = new Employee(1L, "Henry");
map1.put(employee1.getName(), employee1);

Employee employee2 = new Employee(22L, "Annie");
map1.put(employee2.getName(), employee2);

Employee employee3 = new Employee(8L, "John");
map1.put(employee3.getName(), employee3);

Employee employee4 = new Employee(2L, "George");
map2.put(employee4.getName(), employee4);

Employee employee5 = new Employee(3L, "Henry");
map2.put(employee5.getName(), employee5);
```

나중에 사용할 맵의 employee1 및 employee5 항목에 대해 동일한 키가 있음을 유의한다.

## 2. Map.merge()
Java 8은 `java.util.Map` 인터페이스에 새로운 `merge()` 함수를 추가한다.

`merge()` 함수는 다음과 같이 작동한다. 지정된 키가 아직 값과 연결되지 않았거나 값이 null인 경우 키를 지정된 값과 연결한다.

그렇지 않으면 값을 지정된 리매핑 함수의 결과로 바꾼다. 리매핑 함수의 결과가 null이면 결과를 제거한다.

먼저 map1에서 모든 항목을 복사하여 새 HashMap을 구성한다.

```java
Map<String, Employee> map3 = new HashMap<>(map1);
```

다음으로 병합 규칙과 함께 `merge()` 함수를 사용한다.

```java
map3.merge(key, value, (v1, v2) -> new Employee(v1.getId(),v2.getName())
```

마지막으로 map2를 반복 하고 항목을 map3에 병합한다.

```java
map2.forEach(
  (key, value) -> map3.merge(key, value, (v1, v2) -> new Employee(v1.getId(),v2.getName())));
```

프로그램을 실행하고 map3의 내용을 출력한다.

```text
John=Employee{id=8, name='John'}
Annie=Employee{id=22, name='Annie'}
George=Employee{id=2, name='George'}
Henry=Employee{id=1, name='Henry'}
```

결과적으로 **결합된 Map에는 이전 HashMap 항목의 모든 요소가 있다. 중복 키가 있는 항목이 하나의 항목으로 병합되었다.**

또한 마지막 항목의 Employee 개체에 map1의 id가 있고 값이 map2에서 선택되었음을 알 수 있다.

이는 병합 기능에서 정의한 규칙 때문이다.

```java
(v1, v2) -> new Employee(v1.getId(), v2.getName())
```

## 3. Stream.concat()
Java 8의 Stream API는 문제에 대한 쉬운 솔루션을 제공할 수도 있다. 먼저 Map 인스턴스를 하나의 Stream으로 결합해야 한다. 이것이 바로 `Stream.concat()`이 수행하는 작업이다.

```java
Stream combined = Stream.concat(map1.entrySet().stream(), map2.entrySet().stream());
```

여기서 맵 항목 세트를 매개변수로 전달했다.

다음으로 결과를 새 Map으로 수집해야 한다. 이를 위해 `Collectors.toMap()`을 사용할 수 있다.

```java
Map<String, Employee> result = combined.collect(
  Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
```

결과적으로 수집기는 맵의 기존 키와 값을 사용한다. 그러나 이 솔루션은 완벽하지 않다. 수집기가 중복 키가 있는 항목을 만나자마자 IllegalStateException을 발생시킨다.

이 문제를 처리하기 위해 세 번째 "merger" 람다 매개변수를 수집기에 추가하기만 하면 된다.

```java
(value1, value2) -> new Employee(value2.getId(), value1.getName())
```

중복 키가 감지될 때마다 람다식을 사용한다.

마지막으로 다음과 같이 모두 정리한다.

```java
Map<String, Employee> result = Stream.concat(map1.entrySet().stream(), map2.entrySet().stream())
  .collect(Collectors.toMap(
    Map.Entry::getKey, 
    Map.Entry::getValue,
    (value1, value2) -> new Employee(value2.getId(), value1.getName())));
```

이제 코드를 실행하고 결과를 확인한다.

```text
George=Employee{id=2, name='George'}
John=Employee{id=8, name='John'}
Annie=Employee{id=22, name='Annie'}
Henry=Employee{id=3, name='Henry'}
```

"Henry" 키가 있는 중복 항목이 새로운 key-value 쌍으로 병합되었다. 여기서 **새 직원의 id는 map2에서, 값은 map1에서 선택되었다.**

## 4. Stream.of()
Stream API를 계속 사용하려면 `Stream.of()`의 도움으로 Map 인스턴스를 통합 스트림으로 전환할 수 있다.

스트림 작업을 위해 추가 컬렉션을 만들 필요가 없다.

```java
Map<String, Employee> map3 = Stream.of(map1, map2)
  .flatMap(map -> map.entrySet().stream())
  .collect(Collectors.toMap(
    Map.Entry::getKey,
    Map.Entry::getValue,
    (v1, v2) -> new Employee(v1.getId(), v2.getName())));
```

먼저 **map1 과 map2를 단일 스트림으로 변환한다.** 다음으로 스트림을 맵으로 변환한다. 보시다시피 `toMap()`의 마지막 인수는 병합 함수이다. v1 항목에서 id 필드를 선택하고 v2에서 이름을 선택하여 중복 키 문제를 해결한다.

다음은 프로그램을 실행한 후 출력된 map3 인스턴스이다.

```text
George=Employee{id=2, name='George'}
John=Employee{id=8, name='John'}
Annie=Employee{id=22, name='Annie'}
Henry=Employee{id=1, name='Henry'}
```

## 5. Simple Streaming
`stream()` 파이프라인을 사용하여 맵 항목을 조합할 수 있다. 아래 코드 예제는 중복 항목을 무시하여 map2 및 map1에서 항목을 추가하는 방법을 보여준다.

```java
Map<String, Employee> map3 = map2.entrySet()
  .stream()
  .collect(Collectors.toMap(
    Map.Entry::getKey,
    Map.Entry::getValue,
    (v1, v2) -> new Employee(v1.getId(), v2.getName()),
  () -> new HashMap<>(map1)));
```

병합 후 결과는 다음과 같다.

```text
{John=Employee{id=8, name='John'}, 
Annie=Employee{id=22, name='Annie'}, 
George=Employee{id=2, name='George'}, 
Henry=Employee{id=1, name='Henry'}}
```

## 6. StreamEx
JDK에서 제공하는 솔루션 외에도 인기 있는 StreamEx 라이브러리를 사용할 수도 있다.

간단히 말해서 StreamEx는 Stream API의 개선 사항 이며 많은 추가 유용한 메서드를 제공한다. EntryStream 인스턴스를 사용하여 key-value 쌍에서 작동한다.

```java
Map<String, Employee> map3 = EntryStream.of(map1)
  .append(EntryStream.of(map2))
  .toMap((e1, e2) -> e1);
```

아이디어는 맵의 스트림을 하나로 병합하는 것이다. 그런 다음 새 map3 인스턴스에 항목을 수집한다. 중복 키를 처리하기 위한 규칙을 정의하는데 도움이 되므로 `(e1, e2) -> e1` 표현식을 사용하는 것도 중요하다. 이것이 없으면 코드는 IllegalStateException을 발생시킨다.

결과는 다음과 같다.

```text
{George=Employee{id=2, name='George'}, 
John=Employee{id=8, name='John'}, 
Annie=Employee{id=22, name='Annie'}, 
Henry=Employee{id=1, name='Henry'}}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-merge-maps](https://www.baeldung.com/java-merge-maps)
