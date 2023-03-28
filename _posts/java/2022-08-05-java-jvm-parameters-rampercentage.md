---
title: Java JVM 매개변수 InitialRAMPercentage, MinRAMPercentage 및 MaxRAMPercentage
author: dejavuhyo
date: 2022-08-05 10:15:00 +0900
categories: [Language, Java]
tags: [java-jvm-parameters-rampercentage, jvm-parameters-rampercentage, java-jvm-parameters, initialrampercentage, minrampercentage,maxrampercentage, 자바-jvm-매개변수, 자바-매개변수, jvm- 매개변수]
---

## 1. JVM 매개변수
Java 8에 도입된 InitialRAMPercentage, MinRAMPercentage 및 MaxRAMPercentage 매개변수는 Java 애플리케이션의 힙 크기를 구성하는데 도움이 된다.

## 2. -XX:InitialRAMPercentage
InitialRAMPercentage JVM 매개변수를 사용하면 Java 애플리케이션의 초기 힙 크기를 구성 할 수 있다. 물리적 서버 또는 컨테이너의 총 메모리에 대한 백분율로 이중 값으로 전달된다.

예를 들어 전체 메모리가 1GB인 물리적 서버에 대해 `-XX:InitialRAMPercentage=50.0`으로 설정 하면 초기 힙 크기는 약 500MB(1GB의 50%)가 된다.

먼저 JVM에서 IntialRAMPercentage 의 기본값을 확인한다.

```shell
$ docker run openjdk:8 java -XX:+PrintFlagsFinal -version | grep -E "InitialRAMPercentage"
   double InitialRAMPercentage                      = 1.562500                            {product}

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
```

그런 다음 JVM의 초기 힙 크기를 50%로 설정한다.

```shell
$ docker run -m 1GB openjdk:8 java -XX:InitialRAMPercentage=50.0 -XX:+PrintFlagsFinal -version | grep -E "InitialRAMPercentage"
   double InitialRAMPercentage                     := 50.000000                           {product}

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
```

`-Xms` 옵션을 구성할 때 JVM이 `InitialRAMPercentage`를 무시한다.

## 3. -XX:MinRAMPercentage
MinRAMPercentage 매개변수는 이름과 달리 적은 양의 메모리 (200MB 미만)로 실행되는 JVM의 최대 힙 크기를 설정할 수 있다.

먼저 MinRAMPercentage의 기본값을 살펴본다.

```shell
$ docker run openjdk:8 java -XX:+PrintFlagsFinal -version | grep -E "MinRAMPercentage"
   double MinRAMPercentage                      = 50.000000                            {product}

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
```

그런 다음 매개변수를 사용하여 총 메모리가 100MB인 JVM의 최대 힙 크기를 설정한다.

```shell
$ docker run -m 100MB openjdk:8 java -XX:MinRAMPercentage=80.0 -XshowSettings:VM -version

VM settings:
    Max. Heap Size (Estimated): 77.38M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
```

또한 JVM은 작은 메모리 서버/컨테이너에 대한 최대 힙 크기를 설정하는 동안 MaxRAMPercentage 매개변수를 무시한다.

```shell
$ docker run -m 100MB openjdk:8 java -XX:MinRAMPercentage=80.0 -XX:MaxRAMPercentage=50.0 -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 77.38M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
```

## 4. -XX:MaxRAMPercentage
MaxRAMPercentage 매개변수를 사용하면 많은 양의 메모리 (200MB 초과)로 실행되는 JVM의 최대 힙 크기를 설정할 수 있다.

먼저 MaxRAMPercentage의 기본값을 살펴본다.

```shell
$ docker run openjdk:8 java -XX:+PrintFlagsFinal -version | grep -E "MaxRAMPercentage"
   double MaxRAMPercentage                      = 25.000000                            {product}

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
```

그런 다음 매개변수를 사용하여 총 메모리가 500MB인 JVM에 대해 최대 힙 크기를 60%로 설정할 수 있다.

```shell
$ docker run -m 500MB openjdk:8 java -XX:MaxRAMPercentage=60.0 -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 290.00M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
```

마찬가지로 JVM은 대용량 메모리 서버/컨테이너에 대한 MinRAMPercentage 매개변수를 무시한다.

```shell
$ docker run -m 500MB openjdk:8 java -XX:MaxRAMPercentage=60.0 -XX:MinRAMPercentage=30.0 -XshowSettings:vm -version
VM settings:
    Max. Heap Size (Estimated): 290.00M
    Ergonomics Machine Class: server
    Using VM: OpenJDK 64-Bit Server VM

openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
```

## [출처 및 참고]
* [https://www.baeldung.com/java-jvm-parameters-rampercentage](https://www.baeldung.com/java-jvm-parameters-rampercentage)
