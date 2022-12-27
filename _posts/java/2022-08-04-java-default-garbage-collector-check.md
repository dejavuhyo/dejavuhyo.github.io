---
title: Java 기본 Garbage Collector 확인
author: dejavuhyo
date: 2022-08-04 11:15:00 +0900
categories: [Language, Java]
tags: [java-default-garbage-collector, java-garbage-collector, default-garbage-collector, garbage-collector, java-default-gc, default-gc, gc, 자바-기본-가비지-컬렉터, 가비지-컬렉터, 기본-가비지-컬렉터]
---

## 1. Java 기본 GC 확인

### 1) 명령어

```shell
java -XX:+PrintCommandLineFlags -version
```

### 2) Java 1.8
`-XX:+UseParallelGC`로 출력 된다. Java 1.8의 기본 GC는 `ParallelGC`이다.

```shell
-XX:InitialHeapSize=534731584 -XX:MaxHeapSize=8555705344 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
java version "1.8.0_251"
Java(TM) SE Runtime Environment (build 1.8.0_251-b08)
Java HotSpot(TM) 64-Bit Server VM (build 25.251-b08, mixed mode)
```

### 3) Java 11
`-XX:+UseG1GC`로 출력 된다. Java 11의 기본 GC는 `G1GC`이다.

```shell
-XX:G1ConcRefinementThreads=4 -XX:GCDrainStackTargetSize=64 -XX:InitialHeapSize=134217728 -XX:MaxHeapSize=2147483648 -XX:+PrintCommandLineFlags -XX:ReservedCodeCacheSize=251658240 -XX:+SegmentedCodeCache -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC
openjdk version "11.0.3" 2019-04-16 LTS
OpenJDK Runtime Environment Zulu11.31+11-CA (build 11.0.3+7-LTS)
OpenJDK 64-Bit Server VM Zulu11.31+11-CA (build 11.0.3+7-LTS, mixed mode)
```

## [출처 및 참고]
* <https://velog.io/@miz/Java-Default-GC811>
