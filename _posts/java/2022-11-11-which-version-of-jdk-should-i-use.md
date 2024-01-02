---
title: 어떤 버전의 JDK를 사용해야 하는가
author: dejavuhyo
date: 2022-11-11 20:00:00 +0900
categories: [Language, Java]
tags: [openjdk, jdk, java, jdk-release, jdk-version, jdk-lts, adoptium-eclipse-temurin, azul-zulu, bellsoft-liberica-jdk, amazon-corretto, 자바, 자바-버전, 자바-릴리즈, jdk-릴리즈, jdk-장기-지원]
---

## 1. OpenJDK
Java 애플리케이션을 빌드하고 실행하려면 Java Platform, Standard Edition("Java SE") 사양을 구현하는 Java 컴파일러, Java 런타임 라이브러리 및 가상 머신이 필요하다.

![openjdk](/assets/img/2022-11-11-which-version-of-jdk-should-i-use/openjdk.png)

OpenJDK는 Java SE 사양의 오픈 소스 참조 구현이지만 소스 코드일 뿐이다. 이진 배포판은 지원되는 여러 플랫폼에 대해 여러 공급업체에서 제공한다. 이러한 배포판은 라이선스, 상용 지원, 지원되는 플랫폼 및 업데이트 빈도가 다르다.

## 2. Releases
현재 [JDK 릴리스 모델](https://openjdk.org/projects/jdk/)에서는 3월과 9월에 6개월마다 새로운 주요 버전 번호가 포함된 새로운 기능 릴리스가 계획되어 있다. 또한 분기별 버그 수정 업데이트가 있다.

3년마다 9월 릴리스는 LTS(장기 지원) 릴리스로, 최소 3년 동안 업데이트된다.

| JDK Version | Type | Release Date | Highlights | Recommendation |
|:-----:|:-----:|:-----:|:-----:|:-----:|
| 8 | LTS | 03/2014 | Lambdas | Last LTS version under previous release model. Free updates by Oracle [ended](https://www.oracle.com/java/technologies/java-se-support-roadmap.html), but still maintained by others. Upgrade to a 11 or 17 within the next months! |
| 9 | Feature | 09/2017 | Modules | New release model was introduced. EOL. Upgrade to 11 or 17 now! |
| 10 | Feature | 03/2018 | var | EOL. Upgrade to 11 or 17 now! |
| 11 | LTS | 09/2018 | New HTTP Client | Widely used LTS version. Plan upgrade to version 17 within the next months. |
| 12 | Feature | 03/2019 |  | EOL. Upgrade to 17 now! |
| 13 | Feature | 09/2019 |  | EOL. Upgrade to 17 now! |
| 14 | Feature | 03/2020 | Switch expressions | EOL. Upgrade to 17 now! |
| 15 | Feature | 09/2020 | Text blocks | EOL. Upgrade to 17 now! |
| 16 | Feature | 03/2021 | Records | EOL. Upgrade to 17 now! |
| 17 | LTS | 09/2021 | Sealed Classes | Current LTS version. [Update to 17.0.3+ now!](https://neilmadden.blog/2022/04/19/psychic-signatures-in-java/) |
| 18 | Feature | 03/2022 | [UTF-8 by Default](https://openjdk.org/jeps/400) | Stick to 17! It is a short term release without significant features. You might want to check if you run in any issues with "UTF-8 by Default" by setting `-Dfile.encoding=UTF-8` in your current JDK. |
| 19 | Feature | 09/2022 | Preview and incubator features, only | Stick to 17! If you upgraded to 18 before, upgrade now to 19. |

최신 LTS 버전을 유지할지 아니면 최신 기능 릴리스를 사용하고 6개월마다 업그레이드할지 결정해야 한다. 두 가지 방법 모두 좋다. 확실하지 않은 경우 최신 LTS 버전을 사용하면 된다.

OpenJDK 프로젝트 자체는 사양, 소스 코드 및 메일링 목록을 찾을 수 있는 `openjdk.java.net`에서 관리된다. 그러나 다운로드할 수 있는 빌드가 없다.

## 3. Distributions

### 1) OpenJDK builds by Oracle (jdk.java.net)
Oracle은 Linux, macOS 및 Windows용 OpenJDK 빌드를 압축된 아카이브 형식으로 제공한다.

⛔️ *특히 LTS 버전을 사용하려는 경우 Oracle의 OpenJDK 빌드를 사용하지 않는다.*

* Website: [https://www.oracle.com/java/](https://www.oracle.com/java/)

* Releases: [https://jdk.java.net/](https://jdk.java.net/)

* Docker Images: (n/a)

### 2) Oracle Java SE Development Kit (JDK)
Oracle은 OpenJDK와 정확히 동일한 소스인 Oracle Java SE Development Kit(JDK)를 기반으로 하는 OpenJDK의 상용 버전을 제공한다. Oracle은 이러한 빌드에 대한 업데이트를 정기적으로 업데이트하고 보안 패치를 제공한다.

이러한 빌드의 주요 문제는 Oracle의 라이선스 정책이다. 버전 11에서 버전 16까지 빌드는 Oracle Java SE에 대한 Oracle Technology Network License Agreement에 따라 게시되었으며 프로덕션 환경에서 사용하려면 유료 라이선스가 필요하다. 이것이 OpenJDK의 많은 새로운 배포판이 등장한 이유이다.

⛔️ *변호사와 상담하기 전에 Oracle JDK(Java SE Development Kit)를 사용하지 않는다.*

* Website: [https://www.oracle.com/java/](https://www.oracle.com/java/)

* Releases: [https://www.oracle.com/java/technologies/downloads/archive/](https://www.oracle.com/java/technologies/downloads/archive/)

* Docker Images: (n/a)

### 3) Adoptium Eclipse Temurin
Eclipse Adoptium은 Eclipse Foundation의 최상위 프로젝트로, 오픈 소스 소프트웨어에 대한 리소스와 전문적인 거버넌스 모델을 제공한다.

Adoptium Working Group은 Red Hat, IBM, Microsoft, Azul 및 iJUG를 포함하여 Java 기술에 전략적 관심을 두고 있는 주요 기업 및 조직으로 구성된다. 이전 AdoptOpenJDK 프로젝트는 Eclipse Adoptium으로 이동되었다.

Adoptium OpenJDK 빌드는 빌드와 프로젝트를 구별하기 위해 Eclipse Murine이라고 한다.

Eclipse Temurin 빌드는 고품질의 벤더 중립적이며 허가형 라이선스에 따라 TCK 테스트를 거쳤다.

Adoptium은 해당 업스트림 소스가 적극적으로 유지되는 한 LTS 릴리스용 바이너리를 계속 구축할 것이라고 밝혔다.

✅ *Adoptium Eclipse Temurin OpenJDK 빌드를 적극 권장한다.*

* Website: [https://adoptium.net/](https://adoptium.net/)

* Releases: [https://adoptium.net/temurin/archive/](https://adoptium.net/temurin/archive/)

* Docker Images: [https://hub.docker.com/_/eclipse-temurin/](https://hub.docker.com/_/eclipse-temurin/)

### 4) AdoptOpenJDK
AdoptOpenJDK 프로젝트는 Eclipse Adoptium의 전신이며 기본 HotSpot 및 OpenJ9 가상 머신 모두에 대해 고품질 OpenJDK 빌드를 제공했다.

웹사이트와 이전 릴리스는 보관된 릴리스에 액세스할 수 있도록 온라인으로 유지된다.

⛔️ *더 이상 AdoptOpenJDK를 사용하지 않는다. Adoptium Eclipse Temurin을 사용한다.*

* Website: [https://adoptopenjdk.net/](https://adoptopenjdk.net/)

* Releases: [https://adoptopenjdk.net/archive.html?variant=openjdk11&jvmVariant=hotspot](https://adoptopenjdk.net/archive.html?variant=openjdk11&jvmVariant=hotspot)

* Docker Images: [https://hub.docker.com/_/adoptopenjdk](https://hub.docker.com/_/adoptopenjdk)

### 5) Azul Zulu
Azul Systems, Inc.는 전문 Java 기술과 JDK에 대한 상업적 지원을 전문으로 한다. Azul은 높은 업계 평판을 가지고 있으며 Java 플랫폼을 발전시키기 위해 다양한 작업 그룹에 참여하고 있다.

Azul은 많은 운영 체제 및 아키텍처에 대해 Azul Zulu라는 오픈 소스 OpenJDK 빌드를 제공한다. 또한 Azul은 제거된 JRE 및 OpenJFX를 포함한 빌드와 같은 특수 요구 사항에 대한 빌드를 제공한다.

이러한 빌드의 단점은 라이선스 또는 업데이트 정책을 갑자기 변경할 수 있는 단일 회사에 대한 종속성이다.

✅ *OpenJDK의 Azul Zulu 빌드가 좋은 선택이다.*

* Website: [https://www.azul.com/ko/](https://www.azul.com/ko/)

* Releases: [https://www.azul.com/downloads/?package=jdk](https://www.azul.com/downloads/?package=jdk)

* Docker Images: [https://hub.docker.com/r/azul/zulu-openjdk](https://hub.docker.com/r/azul/zulu-openjdk)

### 6) Azul Zing
Azul Zing은 현재 Azul Platform Prime으로 판매되는 상용 JDK이다. 프로덕션 용도로 사용하려면 Azul Systems와 상업적 계약이 필요하다.

Azul Zing은 GC 일시 중지가 문제인 금융 거래 응용 프로그램의 몇 가지 사용 사례를 가지고 있다. 그 외에도 일반 OpenJDK에는 대안이 될 수 있는 무료 낮은 일시 중지 시간 GC [Shenandoah](https://wiki.openjdk.org/display/shenandoah/Main) 및 [ZGC](https://wiki.openjdk.org/display/zgc)가 포함되어 있다.

⚠️ *GC 일시 중지 시간이 문제인 경우 Azul Zing / Azul Platform Prime을 고려한다. 라이센스 없이 프로덕션 환경에서 사용하지 않는다.*

* Website: [https://www.azul.com/](https://www.azul.com/)

* Releases: [https://www.azul.com/products/prime/stream-download/](https://www.azul.com/products/prime/stream-download/)

* Docker Images: (n/a)

### 7) BellSoft Liberica JDK
Azul과 유사하게 BellSoft는 JDK에 대한 전문적인 Java 기술 및 상업적 지원을 전문으로 하고 있다. 또한 BellSoft는 업계에서 높은 평판을 얻고 있으며 Java 플랫폼을 발전시키기 위해 다양한 워킹 그룹에 참가하고 있다.

BellSoft는 거의 모든 운영 체제 및 아키텍처에 대해 Liberica JDK라는 오픈 소스 OpenJDK 빌드를 제공한다.

인기 있는 Spring Boot 프레임워크는 빌드팩의 런타임으로 Liberica JDK를 선택했다.

이러한 빌드의 단점은 라이선스 또는 업데이트 정책을 갑자기 변경할 수 있는 단일 회사에 대한 종속성이다.

✅ *BellSoft Liberica JDK 빌드가 좋은 선택이다.*

* Website: [https://bell-sw.com/](https://bell-sw.com/)

* Releases: [https://bell-sw.com/pages/downloads/?](https://bell-sw.com/pages/downloads/?)

* Docker Images: [https://hub.docker.com/u/bellsoft](https://hub.docker.com/u/bellsoft)

### 8) IBM Semeru Runtime
IBM은 J9라고 하는 자체 버전의 Java Virtual Machine을 개발했으며 Eclipse OpenJ9로 오픈 소스 되었다. 기본 HotSpot Java Virtual Machine의 대안이지만 그다지 인기를 얻지는 못했다.

IBM은 이제 Eclipse OpenJ9 Java Virtual Machine 및 일부 OpenJDK 클래스 라이브러리를 기반으로 Semeru Runtime이라는 빌드를 제공한다. OpenJ9는 메모리 풋프린트가 낮고 공유 클래스로 빠르게 시작 하지만, 핫스팟 가상 머신에 비해 처리량이 적다.

⛔️ *OpenJ9 가상 머신이 필요하다는 것을 알고 있는 경우에만 IBM Semeru Runtime을 사용한다.*

* Website: [https://developer.ibm.com/languages/java/semeru-runtimes/](https://developer.ibm.com/languages/java/semeru-runtimes/)

* Releases: [https://developer.ibm.com/languages/java/semeru-runtimes/downloads/](https://developer.ibm.com/languages/java/semeru-runtimes/downloads/)

* Docker Images: (n/a)

### 9) Amazon Corretto
Oracle이 OpenJDK 빌드에 대한 지원 및 라이선스 정책을 변경한 이후 주요 클라우드 제공업체는 자체적으로 관리되는 OpenJDK 빌드를 구축하고 장기 업데이트를 제공하기로 했다. 분명히 이것은 위험, 특히 Oracle에 대한 소송을 피하기 위한 것이다.

2018년에 AWS는 Corretto, 또 다른 OpenJDK 빌드를 게시했다.

AWS는 최신 OpenJDK 버전의 버그 수정 백 포트를 포함하며 OpenJDK 프로젝트에 아직 통합되지 않은 패치를 추가할 것이라고 주장한다. Amazon은 서비스에 최적화된 대체 암호화 공급자를 구현했다. Corretto에서 기본 암호화 구현으로 사용할 계획이다.

Amazon은 주요 개발 플랫폼에 대한 릴리스와 자체 Amazon Linux 2에 최적화된 버전을 제공한다.

✅ *Corretto 빌드는 특히 AWS의 Amazon Linux 2에서 직접 Java 애플리케이션을 실행하는 경우에 좋은 선택이다.*

* Website: [https://aws.amazon.com/de/corretto/?filtered-posts.sort-by=item.additionalFields.createdDate&filtered-posts.sort-order=desc](https://aws.amazon.com/de/corretto/?filtered-posts.sort-by=item.additionalFields.createdDate&filtered-posts.sort-order=desc)

* Releases: [https://aws.amazon.com/de/corretto/?filtered-posts.sort-by=item.additionalFields.createdDate&filtered-posts.sort-order=desc](https://aws.amazon.com/de/corretto/?filtered-posts.sort-by=item.additionalFields.createdDate&filtered-posts.sort-order=desc)

* Docker Images: [https://hub.docker.com/_/amazoncorretto](https://hub.docker.com/_/amazoncorretto)

### 10) Microsoft Build of OpenJDK
2021년에 Microsoft는 OpenJDK의 Microsoft Build, 또 다른 OpenJDK 빌드를 게시했다.

Microsoft는 최신 OpenJDK 버전의 버그 수정 백 포트를 포함할 수 있으며 OpenJDK 프로젝트에 아직 통합되지 않은 패치를 추가할 것이라고 주장한다.

Microsoft는 주요 개발 플랫폼에 대한 릴리스를 제공한다.

⚠️ *Azure에서 직접 Java 애플리케이션을 실행하는 경우에만 OpenJDK의 Microsoft 빌드를 사용한다.*

* Website: [https://www.microsoft.com/openjdk](https://www.microsoft.com/openjdk)

* Releases: [https://learn.microsoft.com/en-us/java/openjdk/download](https://learn.microsoft.com/en-us/java/openjdk/download)

* Docker Images: [https://learn.microsoft.com/en-us/java/openjdk/containers](https://learn.microsoft.com/en-us/java/openjdk/containers)

### 11) Alibaba Dragonwell
Alibaba는 백 포트와 몇 가지 추가 기능을 포함하는 OpenJDK 빌드를 제공한다.

⛔️ *정부에서 강제하지 않는 한 Alibaba Dragonwell을 사용하지 않는다.*

* Website: [https://dragonwell-jdk.io/](https://dragonwell-jdk.io/)

* Releases: [https://dragonwell-jdk.io/#/index](https://dragonwell-jdk.io/#/index)

* Docker Images: [https://github.com/alibaba/dragonwell11/wiki/Use-Dragonwell-11-docker-images](https://github.com/alibaba/dragonwell11/wiki/Use-Dragonwell-11-docker-images)

### 12) SapMachine
SapMachine은 SAP에서 유지 관리하는 또 다른 OpenJDK 빌드이다.

⚠️ *SAP 서버에서 Java 애플리케이션을 실행하는 경우에만 SapMachine을 사용한다.*

* Website: [https://sap.github.io/SapMachine/](https://sap.github.io/SapMachine/)

* Releases: [https://dragonwell-jdk.io/#/index](https://dragonwell-jdk.io/#/index)

* Docker Images: [https://hub.docker.com/_/sapmachine](https://hub.docker.com/_/sapmachine)

### 13) Red Hat OpenJDK
Red Hat은 LTS 버전용 OpenJDK 빌드를 제공한다.

⚠️ *Red Hat Enterprise Linux에서 직접 Java 애플리케이션을 실행하는 경우에만 Red Hat OpenJDK를 사용한다.*

* Website: [https://developers.redhat.com/products/openjdk/overview](https://developers.redhat.com/products/openjdk/overview)

* Releases: [https://developers.redhat.com/products/openjdk/download](https://developers.redhat.com/products/openjdk/download)

* Docker Images: [https://catalog.redhat.com/software/containers/ubi8/openjdk-11/5dd6a4b45a13461646f677f4](https://catalog.redhat.com/software/containers/ubi8/openjdk-11/5dd6a4b45a13461646f677f4)

### 14) ojdkbuild
프로젝트가 중단되었다. ojdkbuild 프로젝트는 예를 들어 zlib 또는 OpenSSL의 패키지 버전 대신 시스템 라이브러리를 사용하여 Linux OpenJDK 패키지에 가능한 한 동작이 유사한 OpenJDK의 Windows x86_64 바이너리를 제공하는 것을 목표로 했다.

CentOS에 포함된 패키지를 사용했다. 이러한 빌드의 사용 사례는 Windows 시스템에서 Java 소프트웨어를 개발하고 프로덕션 환경의 Linux 서버에 배포하는 것이었다.

⛔️ *프로젝트가 중단 되었으므로 ojdkbuild를 사용하지 않는다.*

* Website: [https://github.com/ojdkbuild/ojdkbuild](https://github.com/ojdkbuild/ojdkbuild)

* Releases: [https://github.com/ojdkbuild/ojdkbuild/releases](https://github.com/ojdkbuild/ojdkbuild/releases)

* Docker Images: (n/a)

### 15) GraalVM
GraalVM은 완전히 호환되는 JDK이지만 다른 모든 빌드와 많이 다르다.

GraalVM은 Oracle에서 개발했다. OpenJDK를 기반으로 하지만 새로운 고성능 컴파일러와 새로운 다중 언어 가상 머신을 포함한다(다른 프로그래밍 언어로 작성된 코드를 실행할 수 있음).

고도로 최적화되고 매우 빠르게 시작되는 플랫폼별 기본 실행 파일을 생성하는 것도 가능하다.

🤷 *검증되지 않았다.*

* Website: [https://www.graalvm.org/](https://www.graalvm.org/)

* Releases: [https://github.com/graalvm/graalvm-ce-builds/releases](https://github.com/graalvm/graalvm-ce-builds/releases)

* Docker Images: [https://github.com/graalvm/container/pkgs/container/graalvm-ce](https://github.com/graalvm/container/pkgs/container/graalvm-ce)

## [출처 및 참고]
* [https://whichjdk.com/](https://whichjdk.com/)
