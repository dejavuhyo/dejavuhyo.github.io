---
title: Java Stream API의 DistinctBy
author: dejavuhyo
date: 2023-12-11 22:00:00 +0900
categories: [Language, Java]
tags: [java-stream, streams-distinctby, 자바-스트림, 스트림-distinctby]
---

## 1. 스트림 API 사용
Stream API는 Object 클래스의 `equals()` 메서드를 기반으로 목록의 다양한 요소를 반환하는 `Unique()` 메서드를 제공한다.

그러나 특정 속성으로 필터링하려는 경우 유연성이 떨어진다. 대안 중 하나는 상태를 유지하는 필터를 작성하는 것이다.

### 1) 상태 저장 필터 사용
가능한 솔루션 중 하나는 상태 저장 조건자를 구현하는 것이다.

```java
public static <T> Predicate<T> distinctByKey(
    Function<? super T, ?> keyExtractor) {
  
    Map<Object, Boolean> seen = new ConcurrentHashMap<>(); 
    return t -> seen.putIfAbsent(keyExtractor.apply(t), Boolean.TRUE) == null; 
}
```

이를 테스트하기 위해 age, email 및 name 속성이 있는 다음 Person 클래스를 사용한다.

```java
public class Person { 
    private int age; 
    private String name; 
    private String email; 
    // standard getters and setters 
}
```

name으로 필터링된 새 컬렉션을 얻으려면 다음을 사용할 수 있다.

```java
List<Person> personListFiltered = personList.stream() 
  .filter(distinctByKey(p -> p.getName())) 
  .collect(Collectors.toList());
```

## 2. Eclipse 컬렉션 사용
[Eclipse 컬렉션](https://eclipse.dev/collections/)은 Java에서 스트림 및 컬렉션을 처리하기 위한 추가 메서드를 제공하는 라이브러리이다.

### 1) ListIterate.distinct() 사용
`ListIterate.distinct()` 메서드를 사용하면 다양한 HashingStrategies를 사용하여 스트림을 필터링할 수 있다. 이러한 전략은 람다 식이나 메서드 참조를 사용하여 정의할 수 있다.

사람의 이름 으로 필터링:

```java
List<Person> personListFiltered = ListIterate
  .distinct(personList, HashingStrategies.fromFunction(Person::getName));
```

또는 사용할 속성이 기본 속성(int, long, double)인 경우 다음과 같은 특수 함수를 사용할 수 있다.

```java
List<Person> personListFiltered = ListIterate.distinct(
  personList, HashingStrategies.fromIntFunction(Person::getAge));
```

### 2) 메이븐 의존성
프로젝트에서 Eclipse 컬렉션을 사용하려면 `pom.xml`에 다음 종속성을 추가해야 한다.

```xml
<dependency> 
    <groupId>org.eclipse.collections</groupId> 
    <artifactId>eclipse-collections</artifactId> 
    <version>8.2.0</version> 
</dependency>
```

## 3. Vavr 사용(Javaslang)
이는 불변 데이터 및 기능 제어 구조를 제공하는 Java 8용 기능 라이브러리이다.

### 1) List.distinctBy 사용
목록을 필터링하기 위해 이 클래스는 포함된 객체의 속성을 기준으로 필터링할 수 있게 해주는 `UniqueBy()` 메서드 가 있는 자체 List 클래스를 제공한다.

```java
List<Person> personListFiltered = List.ofAll(personList)
  .distinctBy(Person::getName)
  .toJavaList();
```

### 2) 메이븐 의존성
프로젝트에서 Vavr을 사용하기 위해 `pom.xml`에 다음 종속성을 추가한다.

```xml
<dependency> 
    <groupId>io.vavr</groupId> 
    <artifactId>vavr</artifactId> 
    <version>0.9.0</version>  
</dependency>
```

## 4. StreamEx 사용
이 라이브러리는 Java 8 스트림 처리에 유용한 클래스와 메서드를 제공한다.

### 1) StreamEx.distinct 사용
제공된 클래스 내에는 구별하려는 속성에 대한 참조를 보낼 수 있는 고유한 메서드가 있는 StreamEx가 있다.

```java
List<Person> personListFiltered = StreamEx.of(personList)
  .distinct(Person::getName)
  .toList();
```

### 2) 메이븐 의존성
프로젝트에서 StreamEx를 사용하기 위해 `pom.xml`에 다음 종속성을 추가한다.

```xml
<dependency> 
    <groupId>one.util</groupId> 
    <artifactId>streamex</artifactId> 
    <version>0.6.5</version> 
</dependency>
```

## [출처 및 참고]
* [https://www.baeldung.com/java-streams-distinct-by](https://www.baeldung.com/java-streams-distinct-by)
