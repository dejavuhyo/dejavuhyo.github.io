---
title: JVM, JRE 및 JDK의 차이점
date: 2021-08-02 06:00:00 +0900
categories: [Language, Java]
tags: [difference-jvm-jre-jdk, jvm-jre-jdk, jvm, jre, jdk, jvm-jre-jdk-차이점]
---

## 1. JVM
JVM(Java Virtual Machine)은 Java 프로그램을 실행하는 가상 머신의 구현이다.

JVM은 먼저 바이트 코드를 해석한다. 그런 다음 메모리 영역에 클래스 정보를 저장한다. 마지막으로 자바 컴파일러가 생성한 바이트 코드를 실행한다.

자체 명령어 세트가 있는 추상 컴퓨팅 머신이며 런타임에 다양한 메모리 영역을 조작한다.

JVM의 구성 요소는 다음과 같다.

* Class Loaders

* Run-Time Data Areas

* Execution Engine

### 1) Class Loaders
JVM의 초기 작업에는 바이트 코드 로드, 확인 및 연결이 포함된다. [클래스 로더](https://www.baeldung.com/java-classloaders)는 이러한 작업을 처리한다.

### 2) Run-Time Data Areas
JVM은 Java 프로그램을 실행하기 위한 다양한 메모리 영역을 정의한다. 이들은 런타임 중에 사용되며 런타임 데이터 영역으로 알려져 있다. 이러한 영역 중 일부는 JVM 시작 시 생성되고 JVM이 종료될 때 소멸되는 반면 일부는 스레드가 생성될 때 생성되고 스레드가 종료될 때 소멸된다.

#### (1) Method Area
기본적으로 메서드 영역은 컴파일된 코드의 저장 영역과 유사하다. 런타임 상수 풀, 필드 및 메서드 데이터, 메서드 및 생성자용 코드, 정규화된 클래스 이름과 같은 구조를 저장한다. JVM은 모든 클래스에 대해 이러한 구조를 저장한다.

영구 생성 공간(PermGen)이라고도 하는 메소드 영역은 JVM이 시작될 때 생성된다. 이 영역에 대한 메모리는 연속적일 필요가 없다. 모든 JVM 스레드는 이 메모리 영역을 공유한다.

#### (2) Heap Area
JVM은 이 영역의 모든 클래스 인스턴스 및 배열에 대한 메모리를 할당한다.

GC(가비지 수집기)는 개체의 힙 메모리를 회수한다. 기본적으로 GC에는 개체에서 메모리를 회수하는 세 단계가 있다. 2개의 마이너 GC와 1개의 메이저 GC이다.

힙 메모리에는 세 부분이 있습니다.

* Eden Space: Young Generation 공간의 일부이다. 객체를 생성할 때 JVM은 이 공간에서 메모리를 할당한다.

* Survivor Space: 이것은 또한 Young Generation 공간의 일부이다. Survivor space는 GC의 마이너 GC 단계에서 살아남은 기존 객체를 포함한다.

* Tenured Space: 이것은 Old Generation space로도 알려져 있다. 오래 살아남은 객체를 보관한다. 기본적으로 Young Generation 객체에 대해 임계 값이 설정되어 있으며 이 임계 값이 충족되면 이러한 객체는 영구 공간으로 이동된다.

JVM은 시작하자마자 힙 영역을 생성한다. JVM의 모든 스레드는 이 영역을 공유한다. 힙 영역에 대한 메모리는 연속적일 필요가 없다.

#### (3) Stack area
데이터를 프레임으로 저장하고 각 프레임은 로컬 변수, 부분 결과 및 중첩 메서드 호출을 저장한다. JVM은 새 스레드를 생성할 때마다 스택 영역을 생성한다. 이 영역은 각 스레드에 대해 비공개이다.

스택의 각 항목을 스택 프레임 또는 활성화 레코드라고 한다. 각 프레임에는 세 부분이 있다.

* Local Variable Array: 메소드의 모든 지역 변수와 매개변수를 포함한다.

* Operand Stack: 중간 계산 결과를 저장하기 위한 작업 공간으로 사용한다.

* Frame Data: 부분 결과를 저장하고 메서드에 대한 반환 값을 저장하고 예외가 발생한 경우 해당 catch 블록 정보를 제공하는 예외 테이블에 대한 참조를 저장하는 데 사용된다.

JVM 스택의 메모리는 연속적일 필요가 없다.

#### (4) PC Registers
각 JVM 스레드에는 현재 실행 중인 명령어의 주소를 저장하는 별도의 PC 레지스터가 있다. 현재 실행 중인 명령어가 기본 메서드의 일부인 경우 이 값은 정의되지 않는다.

#### (5) Native method stacks
네이티브 메소드는 Java 이외의 언어로 작성된 메소드이다.

JVM은 이러한 기본 메소드를 호출하는 기능을 제공한다. 네이티브 메서드 스택은 "C 스택"이라고도 한다. 기본 메서드 정보를 저장한다. 네이티브 메서드가 기계어 코드로 컴파일될 때마다 일반적으로 네이티브 메서드 스택을 사용하여 상태를 추적한다.

JVM은 새 스레드를 생성할 때마다 이러한 스택을 생성한다. 따라서 JVM 스레드는 이 영역을 공유하지 않는다.

### 3) Execution Engine
실행 엔진은 메모리 영역에 있는 정보를 사용하여 명령을 실행한다. 세 부분으로 구성되어 있다.

#### (1) Interpreter
클래스 로더가 바이트 코드를 로드하고 확인하면 인터프리터는 바이트 코드를 한 줄씩 실행한다. 이 실행은 상당히 느리다. 인터프리터의 단점은 하나의 메소드가 여러 번 호출될 때마다 새로운 해석이 필요하다는 것이다.

그러나 JVM은 이러한 단점을 완화하기 위해 JIT 컴파일러를 사용한다.

#### (2) Just-In-Time (JIT) Compiler
JIT 컴파일러는 런타임에 자주 호출되는 메서드의 바이트 코드를 네이티브 코드로 컴파일한다. 따라서 Java 프로그램의 최적화를 담당한다.

JVM은 실행 중인 메소드를 자동으로 모니터링한다. 메서드가 JIT 컴파일에 적합하게 되면 기계어 코드로 컴파일하도록 예약된다. 이 방법을 핫 메서드라고 한다. 기계 코드로의 이 컴파일은 별도의 JVM 스레드에서 발생한다.

결과적으로 현재 프로그램의 실행을 중단하지 않는다. 기계어로 컴파일하면 더 빠르게 실행된다.

#### (3) Garbage Collector
Java는 Garbage Collection을 사용하여 메모리 관리를 처리한다. 힙 메모리를 살펴보고 사용 중인 객체와 사용하지 않는 객체를 식별하고 마지막으로 사용하지 않는 객체를 삭제하는 과정이다.

GC는 데몬 스레드이다. System.gc() 메서드를 사용하여 명시적으로 호출할 수 있지만 즉시 실행되지 않고 JVM이 GC를 호출할 시기를 결정한다.

### 4) Java Native Interface
Java 코드와 기본(C/C++) 라이브러리 간의 인터페이스 역할을 한다.

플랫폼 종속 기능 구현과 같이 Java만으로는 애플리케이션의 요구 사항을 충족하지 못하는 상황이 있다.

이러한 경우 JNI를 사용하여 JVM에서 실행 중인 코드를 호출할 수 있다. 반대로 네이티브 메서드는 JVM에서 실행 중인 코드를 호출할 수 있다.

### 5) Native Libraries
플랫폼별 라이브러리이며 네이티브 메소드의 구현을 포함한다.

## 2. JRE
JRE(Java Runtime Environment)는 Java 애플리케이션을 실행하는 데 사용되는 소프트웨어 구성 요소의 번들이다.

JRE의 핵심 구성 요소는 다음과 같다.

* JVM(Java Virtual Machine) 구현

* Java 프로그램을 실행하는 데 필요한 클래스

* 속성 파일

### 1) Bootstrap Classes
jre/lib/ 아래에서 부트스트랩 클래스를 찾을 수 있다. 이 경로는 부트스트랩 클래스 경로라고도 한다.  여기에는 다음이 포함된다.

* rt.jar의 런타임 클래스

* i18n.jar의 국제화 클래스

* charsets.jar의 문자 변환 클래스

* 기타

Bootstrap ClassLoader는 JVM이 시작될 때 이러한 클래스를 로드한다.

### 2) Extension Classes
Java 플랫폼에 대한 확장을 위한 디렉토리 역할을 하는 jre/lib/extn/에서 확장 클래스를 찾을 수 있다. 이 경로는 확장 클래스 경로라고도 한다.

여기에는 jfxrt.jar의 JavaFX 런타임 라이브러리와 localedata.jar의 java.text 및 java.util 패키지에 대한 로케일 데이터가 포함된다. 사용자는 이 디렉토리에 사용자 정의 jar를 추가할 수도 있다.

### 3) Property Settings
Java 플랫폼은 이러한 속성 설정을 사용하여 구성을 유지 관리한다. 사용법에 따라 /jre/lib/ 안의 다른 폴더에 있다. 여기에는 다음이 포함된다.

* calendar.properties의 달력 구성

* logging.properties의 로깅 구성

* net.properties의 네트워킹 구성

* /jre/lib/deploy/의 배포 속성

* /jre/lib/management/의 관리 속성

### 4) Other Files
위에서 언급한 파일 및 클래스 외에도 JRE에는 다른 문제에 대한 파일도 포함되어 있다.

* jre/lib/security에서 보안 관리

* jre/lib/applet에 애플릿에 대한 지원 클래스를 배치하기 위한 디렉토리

* jre/lib/fonts 및 기타 글꼴 관련 파일

## 3. JDK
JDK(Java Development Kit)는 Java 프로그램을 개발, 컴파일, 디버깅 및 실행하기 위한 환경 및 도구를 제공한다.

JDK의 핵심 구성 요소는 다음과 같다.

* JRE

* 개발 도구

용도에 따라 다음 도구를 분류한다.

### 1) Basic Tools
이러한 도구는 JDK의 기반이 되며 Java 응용 프로그램을 만들고 구축하는 데 사용된다. 이러한 도구 중에서 컴파일, 디버깅, 보관, Javadoc 생성 등을 위한 유틸리티를 찾을 수 있다.

여기에는 다음이 포함된다.

* javac: 클래스 및 인터페이스 정의를 읽고 클래스 파일로 컴파일한다.

* java: Java 애플리케이션을 시작한다.

* javadoc: Java 소스 파일에서 API 문서의 HTML 페이지를 생성한다.

* apt: 지정된 소스 파일 세트에 있는 주석을 기반으로 주석 프로세서를 찾고 실행한다.

* appletviewer: 웹 브라우저 없이 Java 애플릿을 실행할 수 있다.

* jar: Java 애플릿 또는 애플리케이션을 단일 아카이브로 패키징 한다.

* jdb: Java 애플리케이션의 버그를 찾고 수정하는 데 사용되는 명령줄 디버깅 도구

* javah: Java 클래스에서 C 헤더 및 소스 파일을 생성한다.

* javap: 클래스 파일을 디스어셈블하고 클래스 파일에 있는 필드, 생성자 및 메서드에 대한 정보를 표시한다.

* extcheck: 대상 JAR(Java Archive) 파일과 현재 설치된 확장 JAR 파일 간의 버전 충돌을 감지한다.

### 2) Security Tools
여기에는 Java Keystores를 조작하는 데 사용되는 키 및 인증서 관리 도구가 포함된다.

Java Keystore는 인증 인증서 또는 공개 키 인증서를 위한 컨테이너이다. 결과적으로 암호화, 인증 및 HTTPS를 통한 서비스를 위해 Java 기반 애플리케이션에서 자주 사용된다.

또한 시스템에 보안 정책을 설정하고 프로덕션 환경에서 이러한 정책의 범위 내에서 작동할 수 있는 응용 프로그램을 만드는 데 도움을 준다. 여기에는 다음이 포함된다.

* keytool: 키 저장소 항목, 즉 암호화 키 및 인증서를 관리하는 데 도움이 된다.

* jarsigner: 키 저장소 정보를 사용하여 디지털 서명된 JAR 파일 생성한다.

* policytool:  설치의 보안 정책을 정의하는 외부 정책 구성 파일을 관리할 수 있다.

일부 보안 도구는 Kerberos 티켓 관리에도 도움이 된다.

Kerberos는 네트워크 인증 프로토콜이다.

비-보안 네트워크를 통해 통신하는 노드가 안전한 방식으로 서로의 신원을 증명할 수 있도록 티켓을 기반으로 작동한다.

* kinit: Kerberos 티켓 부여에 티켓을 얻고 캐시하는 데 사용된다.

* ktab: 키 테이블에서 원칙 이름과 키 쌍을 관리한다.

* klist: 로컬 자격 증명 캐시 및 키 테이블의 항목을 표시한다.

### 3) Internationalization Tool
국제화는 엔지니어링 변경 없이 다양한 언어 및 지역에 적용할 수 있도록 응용 프로그램을 설계하는 프로세스이다.

이를 위해 JDK는  native2ascii를 제공한다. 이 도구는 JRE에서 지원하는 문자가 있는 파일을 ASCII 또는 유니코드 이스케이프로 인코딩된 파일로 변환한다.

### 4) Remote Method Invocation (RMI) Tools
RMI 도구는 Java 애플리케이션 간의 원격 통신을 가능하게 하여 분산 애플리케이션의 개발 범위를 제공한다.

RMI를 사용하면 한 JVM에서 실행 중인 객체가 다른 JVM에서 실행 중인 객체에서 메소드를 호출할 수 있다. 이러한 도구에는 다음이 포함된다.

* rmic: JRMP(Java Remote Method Protocol) 또는 IIOP(Internet Inter-Orb Protocol)를 사용하여 원격 객체에 대한 스텁, 스켈레톤 및 연결 클래스를 생성한다.

* rmiregistry: 원격 개체 레지스트리를 만들고 시작한다.

* rmid : 활성화 시스템 데몬을 시작한다. 이를 통해 Java Virtual Machine에서 객체를 등록하고 활성화할 수 있다.

* serialver: 지정된 클래스의 직렬 버전 UID를 반환한다.

### 5) Java IDL and RMI-IIOP Tools
Java IDL(인터페이스 정의 언어)은 Java 플랫폼에 CORBA(Common Object-Based Request Broker Architecture) 기능을 추가한다.

이러한 도구를 사용하면 분산된 Java 웹 응용 프로그램이 산업 표준 OMG(Object Management Group) – IDL을 사용하여 원격 네트워크 서비스에서 작업을 호출할 수 있다.

마찬가지로 IIOP(Internet InterORB Protocol)를 사용할 수 있다.

RMI-IIOP, 즉 IIOP를 통한 RMI를 사용하면 RMI API를 통해 CORBA 서버 및 응용 프로그램을 프로그래밍할 수 있다. 따라서 IIOP(Internet InterORB Protocol)를 통해 CORBA 호환 언어로 작성된 두 응용 프로그램 간의 연결을 가능하게 한다.

이러한 도구에는 다음이 포함됩니다.

* tnameserv: 객체 참조를 위한 트리 구조의 디렉토리를 제공하는 임시 이름 지정 서비스

* idlj: 지정된 IDL 파일에 대한 Java 바인딩을 생성하기 위한 IDL-to-Java 컴파일러

* orbd: 클라이언트가 CORBA 환경에서 서버의 영구 객체를 투명하게 찾고 호출할 수 있도록 한다.

* servertool: ORB 데몬(orbd)으로 영구 서버를 등록 또는 등록 취소하고 ORB 데몬에 등록된 영구 서버를 시작 및 종료 하는 등의 명령줄 인터페이스를 제공 한다.

### 6) Java Deployment Tools
이러한 도구는 웹에서 Java 응용 프로그램 및 애플릿을 배포하는 데 도움이 된다. 여기에는 다음이 포함된다.

* pack200: Java gzip 압축기를 사용하여 JAR 파일을 pack200 파일로 변환한다. 

* unpack200: pack200 파일을 JAR 파일로 변환한다.

### 7) Java Plug-in Tool
JDK는 htmlconverter를 제공한다. 또한 Java Plug-in과 함께 사용된다.

한편으로 Java Plug-in은 널리 사용되는 브라우저와 Java 플랫폼 간의 연결을 설정한다. 이 연결의 결과로 웹사이트의 애플릿은 브라우저 내에서 실행될 수 있다.

반면 htmlconverter는 애플릿이 포함된 HTML 페이지를 Java Plug-in용 형식으로 변환하는 유틸리티이다.

### 8) Java Web Start Tool
JDK는  javaws를 제공한다. Java Web Start와 함께 사용할 수 있다.

이 도구를 사용하면 브라우저에서 클릭 한 번으로 Java 응용 프로그램을 다운로드하고 실행할 수 있다. 따라서 설치 프로세스를 실행할 필요가 없다.

### 9) Monitoring and Management Tools
JVM 성능과 리소스 소비를 모니터링하는 데 사용할 수 있는 훌륭한 도구이다. 다음은 몇 가지 사항이다.

* jconsole: Java 애플리케이션을 모니터링하고 관리할 수 있는 그래픽 콘솔을 제공한다.

* jps: 대상 시스템에서 계측된 JVM을 나열한다.

* jstat: JVM 통계 모니터링

* jstatd: 계측된 JVM의 생성 및 종료 모니터링

### 10) Troubleshooting Tools
다음은 문제 해결 작업에 활용할 수 있는 실험적 도구이다.

* info: 지정된 Java 프로세스에 대한 구성 정보를 생성한다.

* jmap: 지정된 프로세스의 공유 객체 메모리 맵 또는 힙 메모리 세부 정보를 인쇄한다.

* jsadebugd: Java 프로세스에 연결하고 디버그 서버 역할을 한다.

* jstack: 주어진 Java 프로세스에 대한 Java 스레드의 Java 스택 추적을 인쇄한다.

## [출처 및 참고]
* <https://www.baeldung.com/jvm-vs-jre-vs-jdk>
