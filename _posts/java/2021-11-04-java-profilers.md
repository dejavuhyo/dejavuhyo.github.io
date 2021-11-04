---
title: Java 프로파일러
author: dejavuhyo
date: 2021-11-04 06:00:00 +0900
categories: [Language, Java]
tags: [java-profilers, profilers, java-프로파일러, 자바-프로파일러, 프로파일러]
---

## 1. Java 프로파일러란
때로는 그냥 실행되는 코드를 쓰는 것만으로는 충분하지 않다. 메모리가 할당되는 방법, 다른 코딩 접근 방식을 사용한 결과, 동시 실행의 의미, 성능 향상을 위한 영역 등 내부적으로 어떤 일이 일어나는지 알고 싶을 수 있다. 프로파일러를 이용하면 된다.

Java Profiler는 JVM 수준에서 Java 바이트코드 구성과 연산을 모니터링하는 도구이다. 이러한 코드 구성 및 작업에는 개체 생성, 반복 실행(재귀 호출 포함), 메서드 실행, 스레드 실행 및 가비지 수집이 포함된다.

주요 Java 프로파일러인 [JProfiler](https://www.ej-technologies.com/products/jprofiler/overview.html), [YourKit](https://www.yourkit.com/java/profiler/), [Java VisualVM](https://visualvm.github.io/) 및 [Netbeans Profiler](https://netbeans.apache.org/kb/docs/java/profiler-intro.html)에 대해 설명한다.

## 2. JProfiler
JProfiler는 많은 개발자들에게 최고의 선택이다. 직관적인 UI를 통해 JProfiler는 시스템 성능, 메모리 사용량, 잠재적인 메모리 누수 및 스레드 프로파일링을 볼 수 있는 인터페이스를 제공한다.

이 정보를 통해 기본 시스템에서 최적화, 제거 또는 변경해야 할 사항을 쉽게 알 수 있다.

JProfiler의 인터페이스는 다음과 같다.

![jprofiler-overview-probing](/assets/img/2021-11-04-java-profilers/jprofiler-overview-probing.png)

대부분의 프로파일러처럼 이 도구를 로컬 및 원격 응용 프로그램에 모두 사용할 수 있다. 즉, 원격 컴퓨터에서 실행되는 Java 애플리케이션을 별도의 설치 없이 프로파일링할 수 있다.

또한 JProfiler는 SQL 및 NoSQL 데이터베이스에 대한 고급 프로파일링을 제공한다. JDBC, JPA/Hibernate, MongoDB, Casandra 및 HBase 데이터베이스 프로파일링을 위한 구체적인 지원을 제공한다.

아래 화면은 현재 연결 목록이 있는 JDBC 프로빙 인터페이스를 보여준다.

![jprofiler-database-probing](/assets/img/2021-11-04-java-profilers/jprofiler-database-probing.png)

데이터베이스와의 상호 작용의 호출 트리에 대해 배우고 누출될 수 있는 연결을 확인하고자 한다면, JProfiler는 이것을 잘 처리한다.

라이브 메모리는 JProfiler의 기능 중 하나로, 응용 프로그램에서 현재 메모리 사용량을 확인할 수 있다. 개체 선언 및 인스턴스 또는 전체 호출 트리의 메모리 사용량을 볼 수 있다.

할당 호출 트리의 경우 라이브 개체, 가비지 수집 개체 또는 둘 다의 호출 트리를 볼 수 있다. 또한 이 할당 트리가 특정 클래스용인지, 패키지용인지, 아니면 모든 클래스용인지 결정할 수 있다.

아래 화면은 인스턴스 수가 있는 모든 개체의 라이브 메모리 사용량을 보여준다.

![jprofiler-live-memory](/assets/img/2021-11-04-java-profilers/jprofiler-live-memory.png)

JProfiler는 Eclipse, NetBeans 및 IntelliJ와 같은 인기 IDE와의 통합을 지원한다. 스냅샷에서 소스 코드로 이동할 수도 있다.

## 3. YourKit
YourKit Java 프로파일러는 다양한 플랫폼에서 실행되며 지원되는 각 운영 체제(Windows, MacOS, Linux, Solaris, FreeBSD 등)에 대해 별도의 설치를 제공한다.

JProfiler와 마찬가지로 YourKit에는 스레드, 가비지 컬렉션, 메모리 사용량 및 메모리 누수를 시각화하는 핵심 기능이 있으며 ssh 터널링을 통한 로컬 및 원격 프로파일링을 지원한다.

다음은 Tomcat 서버 애플리케이션의 메모리 프로파일링 결과를 간략히 살펴본다.

![yourkit-tomcat-profiling-memory](/assets/img/2021-11-04-java-profilers/yourkit-tomcat-profiling-memory.png)

YourKit은 예외 사항을 프로파일링하고 싶을 때도 유용하다. 어떤 유형의 예외가 발생했는지와 각 예외가 발생한 횟수를 쉽게 알 수 있다.

YourKit에는 메서드 또는 스레드의 하위 트리와 같은 특정 코드 영역에 대한 집중적인 프로파일링이 가능한 흥미로운 CPU 프로파일링 기능이 있다. 이것은 만약의 기능을 통해 조건부 프로파일링을 가능하기 때문에 매우 강력하다.

아래 화면은 스레드 프로파일링 인터페이스의 예를 보여준다.

![yourkit-threads-profiling](/assets/img/2021-11-04-java-profilers/yourkit-threads-profiling.png)

또한 YourKit을 사용하여 SQL 및 NoSQL 데이터베이스 호출을 프로파일링할 수 있다. 또한 실행된 실제 쿼리에 대한 보기도 제공한다.

이는 기술적인 고려사항은 아니지만, YourKit의 허용 라이센스 모델은 단일 라이센스 구매뿐만 아니라 다중 사용자 또는 분산 팀에게도 좋은 선택이다.

## 4. Java VisualVM
Java VisualVM은 Java 애플리케이션을 위한 단순하면서도 강력한 프로파일링 도구이다. 기본적으로 이 도구는 JDK(Java Development Kit)와 함께 번들로 제공된다. 이 작업은 JDK에서 제공하는 다른 독립 실행형 도구(예: JConsole, jstat, jstack, jinfo 및 jmap)에 의존한다.

아래는 Java VisualVM을 사용하여 진행 중인 프로파일링 세션의 간단한 개요 인터페이스를 볼 수 있다.

![visualvm-overview](/assets/img/2021-11-04-java-profilers/visualvm-overview.png)

Java VisualVM의 한 가지 흥미로운 장점은 플러그인으로 새로운 기능을 개발하도록 확장할 수 있다는 것이다. 그런 다음 이러한 플러그인을 Java VisualVM의 기본 제공 업데이트 센터에 추가할 수 있다.

Java VisualVM은 로컬 및 원격 프로파일링뿐만 아니라 메모리 및 CPU 프로파일링을 지원한다. 원격 응용 프로그램에 연결하려면 자격 증명(필요한 경우 hostname/IP 및 password)을 제공해야 하지만 ssh 터널링을 지원하지 않는다. 또한 인스턴트 업데이트를 통해 실시간 프로파일링을 사용하도록 선택할 수도 있다(일반적으로 매 2초마다).

아래에서는 Java VisualVM을 사용하여 프로파일링된 Java 애플리케이션의 메모리 전망을 볼 수 있다.

![visualvm-sample-memory](/assets/img/2021-11-04-java-profilers/visualvm-sample-memory.png)

Java VisualVM의 스냅샷 기능을 사용하면 나중에 분석하기 위해 프로파일링 세션의 스냅샷을 만들 수 있다.

## 5. NetBeans Profiler
NetBeans 프로파일러는 Oracle의 오픈 소스 NetBeans IDE와 함께 번들된다.

이 프로파일러는 Java VisualVM과 많은 유사점을 공유하지만, 모든 것을 하나의 프로그램(IDE + Profiler)으로 포장하려면 이 프로파일러를 선택하는 것이 좋다.

위에서 설명한 다른 모든 프로파일러들은 IDE 통합을 강화하기 위한 플러그인을 제공한다.

아래 스크린샷은 NetBeans Profiler 인터페이스의 예를 보여준다.

![netbeans-telemetry-view](/assets/img/2021-11-04-java-profilers/netbeans-telemetry-view.png)

Netbeans Profiler는 경량 개발 및 프로파일링에도 적합하다. NetBeans Profiler는 프로파일링 세션을 구성 및 제어하고 결과를 표시할 수 있는 단일 창을 제공한다. 가비지 수집 빈도를 알 수 있는 고유한 기능을 제공한다.

## 6. 기타 Solid Profilers
여기서 언급할 만한 것은 [Java Mission Control](https://www.oracle.com/java/technologies/jdk-mission-control.html), [New Relict](https://newrelic.com/) 및 [Prefix](https://stackify.com/prefix/)([Stackify](https://stackify.com/)에서 제공)로, 전체 시장 점유율이 낮지만 분명히 언급할 가치가 있다. 예를 들어 Stackify의 Prefix는 Java 응용프로그램뿐만 아니라 다른 웹 응용프로그램 프로파일링에도 적합한 우수한 경량 프로파일링 도구이다.

## [출처 및 참고]
* <https://www.baeldung.com/java-profilers>
