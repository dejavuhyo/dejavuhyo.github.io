---
title: Spring Boot 기본 메모리 설정
author: dejavuhyo
date: 2024-04-15 10:54:00 +0900
categories: [Framework, Spring]
tags: [spring-boot-memory, memory-settings, default-memory-settings, spring-기본-메모리]
---

## 1. 메모리 설정
Java 프로세스 또는 JVM의 메모리 heap, stack, meta-space, JIT code cache, 그리고 shared libraries로 구분된다.

### 1) Heap
힙은 가비지 수집기에 의해 수집될 때까지 객체가 존재하는 메모리의 일부이다.

최소 힙의 기본값은 8Mb 또는 8Mb-1Gb 범위 내 실제 메모리의 1/64 이다.

최대 힙의 기본값은 192MB보다 큰 실제 메모리의 경우 실제 메모리의 1/4이고, 그렇지 않은 경우 실제 메모리의 1/2이다.

힙 내부에는 nursery 크기 제한이 있으며 이를 초과하면 차세대 가비지 수집이 실행된다. 기본값은 플랫폼별로 다르다.

또한 보관 영역 제한이 있다. 이는 도달 시 충분히 오래 지속되는 개체가 젊은 세대에서 이전 세대로 승격되도록 하는 전체 힙 크기의 비율이다. 기본값은 25%이다.

Java 8부터는 모든 클래스 메타데이터가 저장되는 힙의 일부로 메타 공간도 있다. 기본적으로 최소값은 플랫폼에 따라 다르며 최대값은 unlimited 이다.

`-Xns` 매개변수 를 사용하여 nursery 크기 제한을 무시할 수 있다. nursery는 힙의 일부이므로 해당 값은 `-Xmx` 값보다 커서는 안 된다.

```text
java -Xns:10m MyApplication
```

`–XXkeepAreaRatio` 매개변수를 사용하여 유지 영역 제한의 기본값을 재정의할 수도 있다. 예를 들어 10%로 설정할 수 있다.

```java
java -XXkeepAreaRatio:10 MyApplication
```

마지막으로 Linux에서 힙 크기를 확인하는 방법은 다음과 같다.

```java
java -XX:+PrintFlagsFinal -version | grep HeapSize
```

Windows에서 힙 크기를 확인하는 동일한 명령은 다음과 같다.

```java
java -XX:+PrintFlagsFinal -version | findstr HeapSize
```

### 2) Stack
실행을 위해 각 스레드에 제공되는 메모리 양이다. 기본값은 플랫폼별로 다르다.

따라서 `-Xss` 매개변수를 사용하여 스레드 스택 크기를 정의할 수 있다. 예를 들어 512kB에 할당할 수 있다.

```text
java -Xss:512k MyApplication
```

그런 다음 Linux에서 스레드 스택 크기를 확인할 수 있다.

```text
java -XX:+PrintFlagsFinal -version | grep ThreadStackSize
```

또는 Windows 시스템에서도 동일한 작업을 수행한다.

```java
java -XX:+PrintFlagsFinal -version | findstr ThreadStackSize
```

## [출처 및 참고]
* [https://www.baeldung.com/spring-boot-default-memory-settings](https://www.baeldung.com/spring-boot-default-memory-settings)
