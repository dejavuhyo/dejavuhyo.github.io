---
title: Java HashMap 정렬
author: dejavuhyo
date: 2023-04-24 09:25:00 +0900
categories: [Language, Java]
tags: [java-hashmap-sort, hashmap-sort, 자바-해시맵-정렬, 해시맵-정렬]
---

## 1. TreeMap 사용
TreeMap의 키는 자연 순서를 사용하여 정렬된다. 이는 키를 기준으로 키-값 쌍을 정렬하려는 경우 좋은 솔루션이다. 따라서 HashMap의 모든 데이터를 TreeMap으로 푸시하는 것이다.

HashMap을 정의 하고 일부 데이터로 초기화한다.

```java
Map<String, Employee> map = new HashMap<>();

Employee employee1 = new Employee(1L, "Mher");
map.put(employee1.getName(), employee1);
Employee employee2 = new Employee(22L, "Annie");
map.put(employee2.getName(), employee2);
Employee employee3 = new Employee(8L, "John");
map.put(employee3.getName(), employee3);
Employee employee4 = new Employee(2L, "George");
map.put(employee4.getName(), employee4);
```

Employee 클래스의 경우 Comparable을 구현했다.

```java
public class Employee implements Comparable<Employee> {

    private Long id;
    private String name;

    // constructor, getters, setters

    // override equals and hashCode
    @Override
    public int compareTo(Employee employee) {
        return (int)(this.id - employee.getId());
    }
}
```

다음으로 생성자를 사용하여 TreeMap에 항목을 저장한다.

```java
TreeMap<String, Employee> sorted = new TreeMap<>(map);
```

putAll 메서드를 사용하여 데이터를 복사할 수도 있다.

```java
TreeMap<String, Employee> sorted = new TreeMap<>();
sorted.putAll(map);
```

맵 항목이 키별로 정렬되었는지 확인하기 위해 출력한다.

```text
Annie=Employee{id=22, name='Annie'}
George=Employee{id=2, name='George'}
John=Employee{id=8, name='John'}
Mher=Employee{id=1, name='Mher'}
```

키는 자연스러운 순서로 정렬된다.

## 2. ArrayList 사용
ArrayList의 도움으로 맵의 항목을 정렬할 수 있다. 이전 방법과의 주요 차이점은 여기에서 맵 인터페이스를 유지 관리하지 않는다는 것이다.

### 1) 키로 정렬
키 세트를 ArrayList에 로드한다.

```java
List<String> employeeByKey = new ArrayList<>(map.keySet());
Collections.sort(employeeByKey);
```

결과는 다음과 같다.

```text
[Annie, George, John, Mher] 
```

### 2) 값으로 정렬
Employee 개체의 id 필드를 기준으로 맵 값을 정렬하려면 ArrayList를 사용할 수도 있다.

값을 목록에 복사한다.

```java
List<Employee> employeeById = new ArrayList<>(map.values());
```

그런 다음 정렬한다.

```java
Collections.sort(employeeById);
```

Employee가 Comparable 인터페이스를 구현하기 때문에 이것이 작동한다. 그렇지 않으면 `Collections.sort` 호출을 위한 수동 비교기를 정의해야 한다.

결과를 확인하기 위해 employeeById를 출력한다.

```text
[Employee{id=1, name='Mher'}, 
Employee{id=2, name='George'}, 
Employee{id=8, name='John'}, 
Employee{id=22, name='Annie'}]
```

객체는 id 필드로 정렬된다.

## 3. TreeSet 사용
정렬된 컬렉션에서 중복 값을 허용하지 않으려면 TreeSet을 사용하는 좋은 솔루션이 있다.

먼저 초기 맵에 일부 중복 항목을 추가한다.

```java
Employee employee5 = new Employee(1L, "Mher");
map.put(employee5.getName(), employee5);
Employee employee6 = new Employee(22L, "Annie");
map.put(employee6.getName(), employee6);
```

### 1) 키로 정렬
키 항목을 기준으로 맵을 정렬한다.

```java
SortedSet<String> keySet = new TreeSet<>(map.keySet());
```

keySet을 출력한다.

```text
[Annie, George, John, Mher]
```

중복 없이 정렬된 맵 키가 있다.

### 2) 값으로 정렬
마찬가지로 맵 값의 경우 전환 코드는 다음과 같다.

```java
SortedSet<Employee> values = new TreeSet<>(map.values());
```

결과는 다음과 같다.

```text
[Employee{id=1, name='Mher'}, 
Employee{id=2, name='George'}, 
Employee{id=8, name='John'}, 
Employee{id=22, name='Annie'}]
```

출력에 중복이 없다. 이는 equals 및 hashCode를 재정의할 때 사용자 지정 개체와 함께 작동한다.

## 4. 람다와 스트림 사용
Java 8부터 Stream API 및 람다 식을 사용하여 맵을 정렬할 수 있다. 맵의 스트림 파이프라인을 통해 정렬된 메서드를 호출하기만 하면 된다.

### 1) 키로 정렬
키별로 정렬하려면 ComparatorByKey 비교기를 사용한다.

```java
map.entrySet()
  .stream()
  .sorted(Map.Entry.<String, Employee>comparingByKey())
  .forEach(System.out::println);
```

마지막 forEach 단계는 결과를 출력한다.

```text
Annie=Employee{id=22, name='Annie'}
George=Employee{id=2, name='George'}
John=Employee{id=8, name='John'}
Mher=Employee{id=1, name='Mher'}
```

기본적으로 정렬 모드는 오름차순이다.

### 2) 값으로 정렬
물론 Employee 객체를 기준으로 정렬할 수도 있다.

```java
map.entrySet()
  .stream()
  .sorted(Map.Entry.comparingByValue())
  .forEach(System.out::println);
```

보시다시피 위의 코드는 Employee 객체의 id 필드로 정렬된 맵을 출력한다.

```text
Mher=Employee{id=1, name='Mher'}
George=Employee{id=2, name='George'}
John=Employee{id=8, name='John'}
Annie=Employee{id=22, name='Annie'}
```

또한 결과를 새 맵으로 수집할 수 있다.

```java
Map<String, Employee> result = map.entrySet()
  .stream()
  .sorted(Map.Entry.comparingByValue())
  .collect(Collectors.toMap(
    Map.Entry::getKey, 
    Map.Entry::getValue, 
    (oldValue, newValue) -> oldValue, LinkedHashMap::new));
```

결과를 LinkedHashMap에 수집했다. 기본적으로 `Collectors.toMap`은 새로운 HashMap을 반환하지만, HashMap은 반복 순서를 보장하지 않지만 LinkedHashMap은 보장한다.

## 4. Guava 사용
마지막으로 HashMap을 정렬할 수 있는 라이브러리는 Guava이다.

Employee의 Id 필드를 기준으로 지도를 정렬하기 위해 Ordering을 선언한다.

```java
Ordering naturalOrdering = Ordering.natural()
  .onResultOf(Functions.forMap(map, null));
```

이제 ImmutableSortedMap을 사용하여 결과를 설명하기만 하면 된다.

```java
ImmutableSortedMap.copyOf(map, naturalOrdering);
```

그리고 다시 한번 출력은 id 필드로 정렬된 맵이다.

```text
Mher=Employee{id=1, name='Mher'}
George=Employee{id=2, name='George'}
John=Employee{id=8, name='John'}
Annie=Employee{id=22, name='Annie'}
```

## [출처 및 참고]
* [https://www.baeldung.com/java-hashmap-sort](https://www.baeldung.com/java-hashmap-sort)
