---
title: Java 스트림 생성
author: dejavuhyo
date: 2022-09-13 09:00:00 +0900
categories: [Language, Java]
tags: [java-stream-creation, stream-creation, 자바-스트림-생성, 스트림-생성]
---

## 1. 스트림의 생성
스트림 API는 다음과 같은 다양한 데이터 소스에서 생성할 수 있다.

* 컬렉션

* 배열

* 가변 매개변수

* 지정된 범위의 연속된 정수

* 특정 타입의 난수들

* 람다 표현식

* 파일

* 빈 스트림

## 2. 컬렉션
자바에서 제공하는 모든 컬렉션의 최고 상위 조상인 Collection 인터페이스에는 `stream()` 메소드가 정의되어 있다.

따라서 Collection 인터페이스를 구현한 모든 List와 Set 컬렉션 클래스에서도 `stream()` 메소드로 스트림을 생성할 수 있다.

또한, `parallelStream()` 메소드를 사용하면 병렬 처리가 가능한 스트림을 생성할 수 있다.

* 예제

```java
ArrayList<Integer> list = new ArrayList<Integer>();

list.add(4);
list.add(2);
list.add(3);
list.add(1);

// 컬렉션에서 스트림 생성
Stream<Integer> stream = list.stream();
// forEach() 메소드를 이용한 스트림 요소의 순차 접근
stream.forEach(System.out::println);
```

* 실행 결과

```text
4
2
3
1
```

Stream 클래스의 `forEach()` 메소드는 해당 스트림의 요소를 하나씩 소모해가며 순차적으로 요소에 접근하는 메소드이다.

따라서 같은 스트림으로는 `forEach()` 메소드를 한 번밖에 호출할 수 없다.

단, 원본 데이터의 요소를 소모하는 것은 아니므로, 같은 데이터에서 또 다른 스트림을 생성하여 `forEach()` 메소드를 호출하는 것은 가능하다.

## 3. 배열
배열에 관한 스트림을 생성하기 위해 Arrays 클래스에는 다양한 형태의 `stream()` 메소드가 클래스 메소드로 정의되어 있다.

또한, 기본 타입인 `int`, `long`, `double` 형을 저장할 수 있는 배열에 관한 스트림이 별도로 정의되어 있다.

이러한 스트림은 `java.util.stream` 패키지의 `IntStream`, `LongStream`, `DoubleStream` 인터페이스로 각각 제공된다.

* 예제

```java
String[] arr = new String[]{"넷", "둘", "셋", "하나"};

// 배열에서 스트림 생성
Stream<String> stream1 = Arrays.stream(arr);
stream1.forEach(e -> System.out.print(e + " "));
System.out.println();

// 배열의 특정 부분만을 이용한 스트림 생성
Stream<String> stream2 = Arrays.stream(arr, 1, 3);
stream2.forEach(e -> System.out.print(e + " "));
```

* 실행 결과

```text
넷 둘 셋 하나
둘 셋 
```

Arrays 클래스의 `stream()` 메소드는 전체 배열뿐만 아니라 배열의 특정 부분만을 이용하여 스트림을 생성할 수도 있다.

## 4. 가변 매개변수
Stream 클래스의 `of()` 메소드를 사용하면 가변 매개변수(variable parameter)를 전달받아 스트림을 생성할 수 있다.

* 예제

```java
// 가변 매개변수에서 스트림 생성
Stream<Double> stream = Stream.of(4.2, 2.5, 3.1, 1.9);
stream.forEach(System.out::println);
```

* 실행 결과

```text
4.2
2.5
3.1
1.9
```

## 5. 지정된 범위의 연속된 정수
지정된 범위의 연속된 정수를 스트림으로 생성하기 위해 IntStream나 LongStream 인터페이스에는 `range()`와 `rangeClosed()` 메소드가 정의되어 있다.

`range()` 메소드는 명시된 시작 정수를 포함하지만, 명시된 마지막 정수는 포함하지 않는 스트림을 생성한다.

`rangeClosed()` 메소드는 명시된 시작 정수뿐만 아니라 명시된 마지막 정수까지도 포함하는 스트림을 생성한다.

* 예제

```java
// 지정된 범위의 연속된 정수에서 스트림 생성
IntStream stream1 = IntStream.range(1, 4);
stream1.forEach(e -> System.out.print(e + " "));
System.out.println();

IntStream stream2 = IntStream.rangeClosed(1, 4);
stream2.forEach(e -> System.out.print(e + " "));
```

* 실행 결과

```text
1 2 3
1 2 3 4
```

## 6. 특정 타입의 난수들
특정 타입의 난수로 이루어진 스트림을 생성하기 위해 Random 클래스에는 `ints()`, `longs()`, `doubles()`와 같은 메소드가 정의되어 있다.

이 메소드들은 매개변수로 스트림의 크기를 long 타입으로 전달받을 수 있다.

이 메소드들은 만약 매개변수를 전달받지 않으면 크기가 정해지지 않은 무한 스트림(infinite stream)을 반환한다.

이때에는 `limit()` 메소드를 사용하여 따로 스트림의 크기를 제한해야 한다.

* 예제

```java
// 특정 타입의 난수로 이루어진 스트림 생성
IntStream stream = new Random().ints(4);
stream.forEach(System.out::println);
```

* 실행 결과

```text
1072176871
-649065206
133298431
-616174137
```

## 7. 람다 표현식
람다 표현식을 매개변수로 전달받아 해당 람다 표현식에 의해 반환되는 값을 요소로 하는 무한 스트림을 생성하기 위해 Stream 클래스에는 `iterate()`와 `generate()` 메소드가 정의되어 있다.

`iterate()` 메소드는 시드(seed)로 명시된 값을 람다 표현식에 사용하여 반환된 값을 다시 시드로 사용하는 방식으로 무한 스트림을 생성한다.

반면에 `generate()` 메소드는 매개변수가 없는 람다 표현식을 사용하여 반환된 값으로 무한 스트림을 생성한다.

다음 예제는 `iterate()` 메소드를 이용하여 홀수만으로 이루어진 무한 스트림을 생성하는 예제이다.

* 예제

```java
IntStream stream = Stream.iterate(2, n -> n + 2); // 2, 4, 6, 8, 10, ...
```

## 8. 파일
파일의 한 행(line)을 요소로 하는 스트림을 생성하기 위해 `java.nio.file.Files` 클래스에는 `lines()` 메소드가 정의되어 있다.

또한, `java.io.BufferedReader` 클래스의 `lines()` 메소드를 사용하면 파일뿐만 아니라 다른 입력으로부터도 데이터를 행(line) 단위로 읽어 올 수 있다.

* 예제

```java
String<String> stream = Files.lines(Path path);
```

## 9. 빈 스트림
아무 요소도 가지지 않는 빈 스트림은 Stream 클래스의 `empty()` 메소드를 사용하여 생성할 수 있다.

* 예제

```java
// 빈 스트림 생성
Stream<Object> stream = Stream.empty();
System.out.println(stream.count()); // 스트림의 요소의 총 개수를 출력함
```

* 실행 결과

```text
0
```

## [출처 및 참고]
* <http://www.tcpschool.com/java/java_stream_creation>
