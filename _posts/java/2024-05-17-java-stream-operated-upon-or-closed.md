---
title: Java Stream has already been operated upon or closed
author: dejavuhyo
date: 2024-05-17 20:01:00 +0900
categories: [Language, Java]
tags: [java-stream, operated-upon, operated-closed, stream-exception, 스트림-오류]
---

## 1. 원인
Java 8에서 각 Stream 클래스는 일회용 데이터 시퀀스를 나타내며 여러 `I/O` 작업을 지원한다.

```text
IllegalStateException: stream has already been operated upon or closed.
```

스트림은 한 번만 작동해야 합니다(중간 또는 터미널 스트림 작업 호출). Stream 구현은 Stream이 재사용되고 있음을 감지하는 경우 IllegalStateException을 발생시킬 수 있다.

따라서 Stream을 사용하는 단일 작업만 수행할 수 있다. 그렇지 않으면 Stream이 이미 작업되었거나 닫혔다는 예외가 발생한다.

이것이 어떻게 실제 예로 변환될 수 있는지 확인한다.

```java
Stream<String> stringStream = Stream.of("A", "B", "C", "D");
Optional<String> result1 = stringStream.findAny(); 
System.out.println(result1.get()); 
Optional<String> result2 = stringStream.findFirst();
```

결과

```
A
Exception in thread "main" java.lang.IllegalStateException: 
  stream has already been operated upon or closed
```

`#findAny()` 메서드가 호출된 후 stringStream이 닫히므로 Stream에 대한 추가 작업에서는 IllegalStateException이 발생하며 이는 `#findFirst()` 메서드를 호출한 후에 발생한 일이다.

## 2. 해결책
간단히 말해서 해결은 필요할 때마다 새 스트림을 생성하는 것으로 구성된다.

물론 이를 수동으로 수행할 수도 있지만 이때 공급자 기능 인터페이스가 매우 편리해진다.

```java
Supplier<Stream<String>> streamSupplier = () -> Stream.of("A", "B", "C", "D");
Optional<String> result1 = streamSupplier.get().findAny();
System.out.println(result1.get());
Optional<String> result2 = streamSupplier.get().findFirst();
System.out.println(result2.get());
```

결과

```text
A
A
```

`Stream<String>` 유형으로 streamSupplier 객체를 정의했는데, 이는 `#get()` 메소드가 반환하는 유형과 정확히 동일하다. 공급자는 입력을 받지 않고 새 Stream을 반환하는 람다 식을 기반으로 한다.

공급자에서 함수형 메서드 `get()`을 호출하면 새로 생성된 Stream 객체가 반환되며, 이를 통해 안전하게 다른 Stream 작업을 수행할 수 있다.

## [출처 및 참고]
* [https://www.baeldung.com/java-stream-operated-upon-or-closed-exception](https://www.baeldung.com/java-stream-operated-upon-or-closed-exception)
