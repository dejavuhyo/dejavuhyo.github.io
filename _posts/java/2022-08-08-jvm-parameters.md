---
title: JVM 파라미터
author: dejavuhyo
date: 2022-08-08 16:00:00 +0900
categories: [Language, Java]
tags: [java-jvm-parameters, jvm-parameters, jvm-파라미터, 자바-파라미터]
---

## 1. 명시적 힙 메모리 –Xms 및 Xmx 옵션
가장 일반적인 성능 관련 사례 중 하나는 애플리케이션 요구 사항에 따라 힙 메모리를 초기화하는 것이다. 그렇기 때문에 최소 및 최대 힙 크기를 지정해야 한다. 이를 달성하기 위해 아래 매개변수를 사용할 수 있다.

```text
-Xms<heap size>[unit]
-Xmx<heap size>[unit]
```

여기서 단위는 메모리(힙 크기로 표시)가 초기화되는 단위를 나타낸다. 단위는 GB의 경우 'g', MB의 경우 'm', KB의 경우 'k'로 표시할 수 있다 .

예를 들어 JVM에 최소 2GB와 최대 5GB를 할당하려면 다음과 같이 작성해야 한다.

```text
-Xms2G -Xmx5G
```

Java 8부터는 [Metaspace](https://matthung0807.blogspot.com/2019/03/about-g1-garbage-collector-permanent.html)의 크기 가 정의되지 않는다. 전역 제한에 도달하면 JVM이 자동으로 증가하지만 불필요한 불안정성을 극복하기 위해 다음을 사용 하여 Metaspace 크기를 설정할 수 있다.

```text
-XX:MaxMetaspaceSize=<metaspace size>[unit]
```

여기서 metaspace size는 Metaspace에 할당하려는 메모리의 양을 나타낸다.

Oracle 지침에 따라 사용 가능한 총 메모리 다음으로 가장 영향력 있는 요소는 Young Generation 용으로 예약된 힙의 비율이다. 기본적으로 YG의 최소 크기는 1310MB이고 최대 크기는 무제한이다.

명시적으로 할당할 수 있다.

```text
-XX:NewSize=<young size>[unit] 
-XX:MaxNewSize=<young size>[unit]
```

## 2. Garbage Collection
애플리케이션의 더 나은 안정성을 위해서는 올바른 가비지 컬렉션 알고리즘을 선택하는 것이 중요하다.

JVM에는 4가지 유형의 GC 구현이 있습니다.

* 직렬 쓰레기 수집기

* 병렬 가비지 수집기

* CMS 가비지 컬렉터

* G1 가비지 컬렉터

이러한 구현은 아래 매개변수를 사용하여 선언할 수 있다.

```text
-XX:+UseSerialGC
-XX:+UseParallelGC
-XX:+USeParNewGC
-XX:+UseG1GC
```

## 3. GC 로깅
애플리케이션 상태를 엄격하게 모니터링하려면 항상 JVM의 가비지 컬렉션 성능을 확인해야 한다. 이를 수행하는 가장 쉬운 방법은 GC 활동을 사람이 읽을 수 있는 형식으로 기록하는 것이다.

다음 매개변수를 사용하여 GC 활동을 기록할 수 있다.

```text
-XX:+UseGCLogFileRotation 
-XX:NumberOfGCLogFiles=< number of log files > 
-XX:GCLogFileSize=< file size >[ unit ]
-Xloggc:/path/to/gc.log
```

UseGCLogFileRotation은 log4j, s4lj 등과 같은 로그 파일 롤링 정책을 지정한다. NumberOfGCLogFiles는 단일 애플리케이션 수명 주기 동안 쓸 수 있는 최대 로그 파일 수를 나타낸다.

GCLogFileSize는 파일의 최대 크기를 지정한다. 마지막으로 loggc는 위치를 나타낸다.

여기서 주목해야 할 점은 GC 로그에서 날짜별 타임스탬프를 인쇄하는데 사용할 수 있는 두 가지 JVM 매개변수(`-XX:+PrintGCTimeStamps` 및 `-XX:+PrintGCDateStamps`)가 더 있다는 것이다.

예를 들어 최대 100개의 GC 로그 파일을 할당하고 각각의 최대 크기는 50MB이고 `/home/user/log/` 위치에 저장하려는 경우 아래 구문을 사용할 수 있다.

```text
-XX:+UseGCLogFileRotation  
-XX:NumberOfGCLogFiles=10
-XX:GCLogFileSize=50M 
-Xloggc:/home/user/log/gc.log
```

그러나 문제는 하나의 추가 데몬 스레드가 항상 백그라운드에서 시스템 시간을 모니터링하는데 사용된다는 것이다. 이 동작은 성능 병목 현상을 일으킬 수 있다. 그렇기 때문에 프로덕션에서는 항상 이 매개변수를 사용하지 않는 것이 좋다.

## 4. 메모리 부족 처리
[큰 응용 프로그램에서 메모리 부족 오류가 발생](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/OutOfMemoryError.html)하여 응용 프로그램 충돌이 발생하는 것은 매우 일반적이다 . 이는 매우 중요한 시나리오이며 문제를 해결하기 위해 복제하기가 매우 어렵다.

이것이 JVM이 힙 메모리를 나중에 누수를 찾는데 사용할 수 있는 실제 파일로 덤프하는 몇 가지 매개변수와 함께 제공되는 이유이다.

```text
-XX:+HeapDumpOnOutOfMemoryError 
-XX:HeapDumpPath=./java_pid<pid>.hprof
-XX:OnOutOfMemoryError="< cmd args >;< cmd args >" 
-XX:+UseGCOverheadLimit
```

주의할 몇 가지 사항은 아래와 같다.

* HeapDumpOnOutOfMemoryError는 OutOfMemoryError의 경우 힙을 실제 파일로 덤프하도록 JVM에 지시한다.

* HeapDumpPath는 파일이 기록될 경로를 나타낸다. 모든 파일 이름을 지정할 수 있다. 그러나 JVM이 이름에서 `<pid>`  태그를 찾으면 메모리 부족 오류를 일으키는 현재 프로세스의 프로세스 ID가 `.hprof` 형식으로 파일 이름에 추가된.

* OnOutOfMemoryError는 메모리 부족 오류가 발생한 경우 실행할 긴급 명령을 실행하는데 사용된다. cmd 인수 공간에 적절한 명령을 사용해야 한다. 예를 들어 메모리 부족이 발생하는 즉시 서버를 다시 시작하려면 다음 매개변수를 설정할 수 있다.

```text
-XX:OnOutOfMemoryError="shutdown -r"
```

* UseGCOverheadLimit는 OutOfMemory 오류가 발생하기 전에 GC에서 소비된 VM 시간의 비율을 제한하는 정책이다.

## 5. 32/64비트
32비트와 64비트 패키지가 모두 설치된 OS 환경에서 JVM은 자동으로 32비트 환경 패키지를 선택한다. 환경을 수동으로 64비트로 설정하려면 아래 매개변수를 사용하여 설정할 수 있다.

```text
-d<OS bit>
```

OS 비트는 32 또는 64 일 수 있다 . [이에 대한 자세한 정보](https://www.oracle.com/java/technologies/hotspotfaq.html#64bit_layering)를 찾을 수 있다.

## 6. 기타

* `-server`: "Server Hotspot VM"을 사용하도록 설정한다. 이 매개 변수는 기본적으로 64비트 JVM에서 사용된다.

`-XX:+UseStringDeduplication`: Java 8u20은 동일한 String의 인스턴스를 너무 많이 생성하여 메모리의 불필요한 사용을 줄이기 위해 이 JVM 매개 변수를 도입했다. 이 매개 변수는 중복된 String 값을 단일 글로벌 char[] 배열로 줄임으로써 힙 메모리를 최적화한다.

`-XX:+UseLWPSynchronization`: 스레드 기반 동기화 대신 LWP(Light Weight Process) 기반 동기화 정책을 설정한다.

`-XX:LargePageSizeInBytes`: Java 힙에 사용되는 큰 페이지 크기를 설정한다. 이것은 `GB/MB/KB` 단위로 인수를 사용한다. 페이지 크기가 클수록 가상 메모리 하드웨어 리소스를 더 잘 사용할 수 있다. 그러나 이는 PermGen을 위한 더 큰 공간 크기를 유발하여 Java 힙 공간의 크기를 강제로 줄일 수 있다.

`-XX:MaxHeapFreeRatio`: 축소를 방지하기 위해 GC 이후 힙 사용 가능 최대 비율을 설정한다.

`-XX:MinHeapFreeRatio`: 확장을 방지하기 위해 GC 이후 힙 사용 가능 최소 비율을 설정한다. 힙 사용량을 모니터링하려면 JDK와 함께 제공된 VisualVM을 사용할 수 있다.

`-XX:SurvivorRatio`: `eden/Survivor` 공간 크기 비율. 예를 들어 `-XX:SurvivorRatio=6`은 각 Survivor 공간과 eden 공간의 비율 을 `1:6`으로 설정한다.

`-XX:+UseLargePages`: 시스템에서 지원하는 경우 대용량 페이지 메모리를 사용한다. 이 JVM 매개변수를 사용 하면 OpenJDK 7이 충돌하는 경향이 있다.

`-XX:+UseStringCache`: 문자열 풀에서 사용할 수 있는 일반적으로 할당된 문자열의 캐싱을 활성화한다.

`-XX:+UseCompressedStrings`:순수한 ASCII 형식으로 표현할 수 있는 String 객체에 `byte[]` 유형을 사용한다.

`-XX:+OptimizeStringConcat`: 가능한 경우 문자열 연결 작업을 최적화한다.

## [출처 및 참고]
* [https://www.baeldung.com/jvm-parameters](https://www.baeldung.com/jvm-parameters)
* [https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html)
