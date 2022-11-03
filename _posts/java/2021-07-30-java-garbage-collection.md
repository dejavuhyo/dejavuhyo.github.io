---
title: Java Garbage Collection
author: dejavuhyo
date: 2021-07-30 06:00:00 +0900
categories: [Language, Java]
tags: [java-garbage-collection, garbage-collection, garbage-collectors, 자바-가비지-컬렉션, 가비지-컬렉션, 가비지-수집, 가비지-수집기]
---

## 1. Java Virtual Machine(JVM)
C 나 C++에서는 OS 레벨의 메모리에 직접 접근하기 때문에 free()라는 메소드를 호출하여 할당받았던 메모리를 명시적으로 해제해주어야 한다. 그렇지 않으면 memory leak이 발생하게 되고, 현재 실행 중인 프로그램에서 memory leak 이 발생하면 다른 프로그램에도 영향을 끼칠 수 있다.

반면, Java는 OS의 메모리 영역에 직접적으로 접근하지 않고 JVM이라는 가상머신을 이용해서 간접적으로 접근한다. JVM은 C로 쓰여진 또 다른 프로그램인데, 오브젝트가 필요해지지 않는 시점에서 알아서 free()를 수행하여 메모리를 확보한다. 웹 애플리케이션을 만들 때 모든 것을 다 직접 개발하여 쓰기보다 검증된 라이브러리나 프레임워크를 이용하는 것이 더 안전하고 편리한 것처럼, 메모리 관리라는 까다로운 부분을 Java 가상머신에 모두 맡겨버리는 것이다.

프로그램 실행 시 JVM 옵션을 주어서 OS에 요청한 사이즈만큼의 메모리를 할당받아서 실행하게 된다. 할당받은 이상의 메모리를 사용하게 되면 에러가 나면서 자동으로 프로그램이 종료된다. 그러므로 현재 프로세스에서 메모리 누수가 발생하더라도 현재 실행 중인 것만 죽고, 다른 것에는 영향을 주지 않는다.

이렇게 Java는 가상머신을 사용함으로써(운영체제로 부터 독립적이라는 장점 외에도) OS 레벨에서의 memory leak 은 불가능하게 된다는 장점이 있다.

Java가 메모리 누수현상을 방지하는 또 다른 방법이 Garbage Collection이다.

## 2. Garbage Collection 소개
Garbage Collection은 메모리에서 가비지를 찾아 삭제하는 작업을 다루고 있는 것으로 보인다. 그러나 실제로 Garbage Collection은 JVM 힙 공간에서 사용 가능한 모든 개체를 추적하고 사용되지 않는 개체를 제거한다.

* **Mark:** 가비지 수집기가 사용 중인 메모리 조각과 사용하지 않는 메모리 조각을 식별한다.

* **Sweep:** 이 단계는 "mark" 단계에서 식별된 개체를 제거한다.

### 1) 장점

* 사용되지 않은 메모리 공간은 GC에서 자동으로 처리되므로 수동 메모리 할당/할당 해제 처리 없음

* Dangling Pointer 처리 오버헤드 없음

* 자동 [메모리 누수](https://en.wikipedia.org/wiki/Memory_leak) 관리(GC만으로는 메모리 누수에 대한 완벽한 입증 솔루션을 보장할 수 없지만, 상당 부분을 관리한다.)

### 2) 단점

* JVM은 개체 참조 생성/삭제를 추적해야 하므로 이 작업은 원래 애플리케이션보다 더 많은 CPU 성능을 필요로 한다. 대용량 메모리가 필요한 요청의 성능에 영향을 줄 수 있다.

* 프로그래머는 더 이상 필요하지 않은 객체를 해제하는 데 사용되는 CPU 시간 스케줄링을 제어할 수 없다.

* 일부 GC 구현을 사용하면 애플리케이션이 예기치 않게 중지될 수 있다.

* 자동 메모리 관리는 적절한 수동 메모리 할당/할당 해제만큼 효율적이지 않다.

## 4. Garbage Collector 유형

### 1) Serial Garbage Collector
이것은 기본적으로 단일 스레드로 작동하므로 가장 간단한 GC 구현이다. 따라서 이 GC 구현은 실행될 때 모든 애플리케이션 스레드를 중지한다. 따라서 서버 환경과 같은 다중 스레드 애플리케이션에서 사용하는 것은 좋은 생각이 아니다.

그러나 QCon 2012에서 Twitter 엔지니어들이 Serial Garbage Collector의 성능에 대해 훌륭한 이야기를 나눈 적이 있다. 이는 이 수집기를 더 잘 이해할 수 있는 좋은 방법이다.

Serial GC는 짧은 일시 중지 시간 요구사항이 없고 클라이언트 스타일 시스템에서 실행되는 대부분의 응용 프로그램에 대해 선택 가능한 가비지 수집기이다. Serial Garbage Collector를 활성화하려면 다음 인수를 사용한다.

```text
java -XX:+UseSerialGC -jar Application.java
```

### 2) Parallel Garbage Collector
JVM의 기본 GC이며, 처리량 수집기라고도 한다. Serial Garbage Collector와 달리 이 수집기는 힙 공간을 관리하는 데 다중 스레드를 사용합니다. 그러나 GC를 수행하는 동안 다른 응용 프로그램 스레드도 중지된다.

이 GC를 사용하면 최대 가비지 수집 스레드를 지정하고 일시 중지 시간, 처리량 및 설치 공간(힙 크기)을 지정할 수 있다.

가비지 수집기 스레드 수는 명령줄 옵션 `XX:ParallelGCThreads=<N>`로 제어할 수 있다.

최대 일시 중지 시간 목표(두 GC 사이의 간격 [밀리초])는 명령줄 옵션 `-XX:MaxGCPauseMillis=<N>`로 지정한다.

가비지 수집에 소요된 시간과 가비지 수집 외부에서 소요된 시간을 최대 처리량 목표라고 하며 명령줄 옵션 `-XX:GCTimeRatio=<N>`로 지정한다.

최대 힙 공간(프로그램 실행 중에 필요한 힙 메모리 양)은 `-Xmx<N>` 옵션을 사용하여 지정한다.

Parallel Garbage Collector를 활성화하려면 다음 인수를 사용한다.

```text
java -XX:+UseParallelGC -jar Application.java
```

### 3) CMS Garbage Collector
Concurrent Mark Sweep(CMS) 구현은 가비지 수집에 여러 가비지 수집기 스레드를 사용한다. 이 기능은 가비지 수집 일시 중지 시간을 단축하고, 응용 프로그램이 실행되는 동안 가비지 수집기와 프로세서 리소스를 공유할 수 있도록 설계되었다.

간단히 말해, 이 유형의 GC를 사용하는 응용 프로그램은 평균 응답 속도가 느리지만 가비지 수집을 수행하기 위해 응답을 중지하지 않는다.

여기서 주목할 점은 이 GC가 동시이므로 동시 프로세스가 작동하는 동안 System.gc()를 사용하는 것과 같은 명시적 가비지 수집을 호출하면 Concurrent Mode Failure/Interruption이 발생한다는 것이다.

총 시간의 98% 이상이 CMS 가비지 수집에 소요되고 힙의 2% 미만이 복구되면 CMS 수집기에 의해 OutOfMemoryError가 발생한다. 필요한 경우 명령줄에 `-XX:-UseGCOverheadLimit` 옵션을 추가하여 이 기능을 사용하지 않도록 설정할 수 있다.

이 수집기에는 Java SE 8에서 더 이상 사용되지 않고 향후 주요 릴리스에서 제거될 수 있는 증분 모드로 알려진 모드도 있다.

CMS Garbage Collector를 활성화하기 위해 다음 플래그를 사용할 수 있다.

```text
java -XX:+UseParNewGC -jar Application.java
```

Java 9부터는 CMS garbage collector가 사용되지 않는다. 따라서 JVM은 다음과 같은 경고 메시지를 출력한다.

```text
>> java -XX:+UseConcMarkSweepGC --version
Java HotSpot(TM) 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated 
in version 9.0 and will likely be removed in a future release.
java version "9.0.1"
```

또한 Java 14는 CMS 지원을 완전히 중단했다.

```text
>> java -XX:+UseConcMarkSweepGC --version
OpenJDK 64-Bit Server VM warning: Ignoring option UseConcMarkSweepGC; 
support was removed in 14.0
openjdk 14 2020-03-17
```

### 4) G1 Garbage Collector
G1(Garbage First) Garbage Collector는 메모리 공간이 큰 다중 프로세서 시스템에서 실행되는 응용 프로그램을 위해 설계되었다. 이 기능은 DK7 Update 4 이후 릴리스부터 사용할 수 있다.

G1 수집기는 성능이 더 효율적이기 때문에 CMS 수집기를 대체한다.

다른 수집기와 달리 G1 수집기는 힙을 동일한 크기의 힙 영역 세트로 분할하며, 각각은 가상 메모리의 연속 범위이다. 가비지 수집을 수행할 때 G1은 개체의 활성 상태를 결정하기 위해 동시 전역 표시 단계(즉, 표시로 알려진 단계 1)를 표시 합니다.

표시 단계가 완료된 후 G1 은 대부분 비어 있는 영역을 알고 있다. 일반적으로 상당한 양의 여유 공간(즉, 스위핑으로 알려진 2단계)을 생성하는 이러한 영역에서 먼저 수집된다. 그래서 이 가비지 수집 방법을 Garbage-First라고 하는 이유이다.

G1 Garbage Collector 를 활성화 하기 위해 다음 인수를 사용한다.

```text
java -XX:+UseG1GC -jar Application.java
```

### 5) Java 8 변경 사항
Java 8u20은 동일한 문자열의 인스턴스를 너무 많이 생성하여 불필요한 메모리 사용을 줄이기 위해 JVM 매개변수를 하나 더 도입했다. 이것은 중복 문자열 값을 전역 단일 char[] 배열로 제거하여 힙 메모리를 최적화한다.

이 매개변수는 `-XX:+UseStringDeduplication`을 JVM 매개변수로 추가하여 활성화할 수 있다.

### 6) Z Garbage Collector
ZGC(Z Garbage Collector)는 Linux의 실험 옵션으로 Java 11에서 첫 선을 보인 확장 가능한 대기 시간이 짧은 가비지 수집기이다. JDK 14는 Windows 및 macOS 운영 체제에서 ZGC를 도입했다. ZGC는 Java 15 프로덕션 상태를 얻었다.

ZGC는 10ms 이상 애플리케이션 스레드 실행을 중단하지 않고 모든 비용이 많이 드는 작업을 동시에 수행하므로 대기 시간이 짧은 애플리케이션에 적합하다. 스레드가 실행 중이고 힙 사용량을 추적하는데 사용되는 경우 동시 작업을 수행하기 위해 컬러 포인터가 있는 로드 장벽을 사용합니다.

참조 색상(색상 포인터)은 ZGC의 핵심 개념이다. 이는 ZGC가 참조의 일부 비트(메타데이터 비트)를 사용하여 개체의 상태를 표시함을 의미한다. 8MB에서 16TB에 이르는 힙을 처리한다. 또한 힙, 라이브 세트 또는 루트 세트 크기에 따라 일시 중지 시간이 증가하지 않는다.

G1과 마찬가지로 Z Garbage Collector는 힙 영역의 크기가 다를 수 있다는 점을 제외하고 힙을 분할한다.

Z Garbage Collector를 활성화하기 위해 JDK 15 미만의 JDK 버전에서 다음 인수를 사용할 수 있다.

```text
java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC Application.java
```

버전 15부터는 실험 모드가 필요하지 않다.

```text
java -XX:+UseZGC Application.java
```

ZGC는 기본 가비지 컬렉터가 아니다.

## [출처 및 참고]
* <https://www.baeldung.com/jvm-garbage-collectors>
* <https://en.wikipedia.org/wiki/Memory_leak>
* <https://yaboong.github.io/java/2018/06/09/java-garbage-collection/>
