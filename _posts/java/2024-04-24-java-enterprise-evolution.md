---
title: Java EE vs J2EE vs Jakarta EE
author: dejavuhyo
date: 2024-04-24 15:44:00 +0900
categories: [Language, Java]
tags: [java-ee, j2ee, jakarta-ee, java-enterprise]
---

## 1. History
Java의 첫 번째 버전에서 Java 엔터프라이즈 확장은 단순히 핵심 JDK의 일부 였다.

그러다가 1999년 Java 2의 일부로 이러한 확장이 표준 바이너리에서 분리되어 J2EE, 즉 [Java 2 Platform Enterprise Edition](https://www.oracle.com/java/technologies/appmodel.html)이 탄생했다. 2006년까지 그 이름을 유지할 예정이었다.

2006년 Java 5의 경우 J2EE는 Java EE 또는 Java Platform Enterprise Edition으로 이름이 변경되었다. 그 이름은 중대한 일이 발생한 2017년 9월까지 계속 유지되었다.

2017년 9월 [Oracle은 Java EE에 대한 권한을 Eclipse Foundation에 넘기기로 결정](https://www.redhat.com/en/blog/java-ee-moves-eclipse)했다 (언어는 여전히 Oracle이 소유하고 있다).

## 2. 전환 중
실제로 Eclipse Foundation은 법적으로 Java EE의 이름을 바꿔야 했다. 이는 Oracle이 "Java" 브랜드에 대한 권리를 갖고 있기 때문이다.

그래서 새 이름을 선택하기 위해 커뮤니티는 투표를 통해 Jakarta EE를 선택했다. 어떤 면에서는 여전히 JEE이다.

| Version | Date |
|:-----:|:-----:|
| J2EE 1.2 | December 1999 |
| J2EE 1.3 | September 2001 |
| J2EE 1.4 | November 2003 |
| Java EE 5 | May 2006 |
| Java EE 6 | December 2009 |
| Java EE 7 | April 2013 |
| Java EE 8 | August 2017 |
| Jakarta EE | February 2018 |

하지만 이것은 여전히 ​​진화하는 이야기이고, 먼지가 완전히 가라앉지 않았다.

예를 들어 Oracle은 소스 코드를 오픈 소스로 제공했지만 모든 문서를 오픈 소스로 제공하지는 않았다. 예를 들어 JMS 및 EJB와 관련된 오픈 소스 문서를 까다롭게 만드는 법적 문제로 인해 이 문제에 대해 여전히 많은 논의가 있다.

새로운 Eclipse Foundation 문서가 원본을 참조할 수 있는지는 아직 확실하지 않다.

또한 흥미롭게도 Eclipse Foundation은 javax 네임스페이스를 사용하여 새로운 Java 패키지를 생성할 수 없지만 기존 클래스 아래에 새 클래스와 하위 클래스를 생성할 수 있다.

전환은 또한 Jakarta EE에 사양을 추가하기 위한 새로운 프로세스를 의미한다. 더 잘 이해하기 위해 Oracle에서의 프로세스가 어땠는지, Eclipse Foundation에서는 어떻게 변경되었는지 살펴본다.

## 3. Future
역사적으로 기능을 "EE"로 만들려면 사양, 참조 구현, 테스트라는 세 가지가 필요했다. 이 세 가지 항목은 커뮤니티의 누구든지 제공할 수 있으며 실행 위원회는 이러한 항목을 언어에 추가할 준비가 되었는지 결정한다.

이전 프로세스를 더 잘 이해하기 위해 JSR, Glassfish 및 TCK가 무엇인지, 그리고 이들이 새로운 EE 기능을 어떻게 구현했는지 자세히 살펴본다.

또한 미래에 무엇을 기대하게 될지 확인한다.

### 1) JCP와 지금은 EFSP
과거에는 새로운 EE 기능이 탄생하는 프로세스를 JCP(Java Community Process)라고 했다.

Java SE는 오늘날에도 여전히 JCP를 사용한다. 그러나 EE가 소유권을 Oracle에서 Eclipse Foundation으로 변경했기 때문에 이를 위한 새롭고 별도의 프로세스가 있다. 이는 [EFSP](https://www.eclipse.org/projects/efsp/)(Eclipse Foundation Specification Process)이며 [Eclipse 개발 프로세스](https://www.eclipse.org/projects/dev_process/)의 확장이다.

그러나 대부분 "투명성, 개방성, 공유 부담 및 공급업체 중립성"과 관련하여 몇 가지 중요한 차이점이 있다. 예를 들어 EFSP 조직자는 공급업체 중립적인 협업 작업 그룹, 셀프 서비스 인증 프로세스, 능력주의로 운영 및 관리되는 조직을 구상한다.

### 2) JSRs
JCP에서 EE에 기능을 추가하는 첫 번째 단계는 JSR 또는 Java 사양 요청을 생성하는 것이었다. JSR은 EE 기능의 인터페이스와 약간 비슷했다. JCP 집행 위원회는 완성된 JSR을 검토하고 승인한 다음 JSR 기여자가 이를 코딩하여 커뮤니티에 제공한다.

이에 대한 좋은 예는 [JSR-339](https://jcp.org/en/jsr/detail?id=339)(JAX-RS)이다. JAX-RS는 원래 2011년에 제안되어 2012년 JCP의 승인을 받고 2013년에 최종 출시되었다.

사양이 논의되는 동안 커뮤니티는 항상 의견을 나눌 수 있었지만 [JSR 310](https://jcp.org/en/jsr/detail?id=310), [java.time](https://www.baeldung.com/java-8-date-time-intro) 및 [Joda Time](https://www.baeldung.com/joda-time)의 경우와 같은 구현 우선 접근 방식이 더 널리 수용되는 기능과 API를 생성하는 경향이 있는 것으로 나타났다.

따라서 EFSP는 명시된 목표에 이러한 코드 우선 관점을 반영한다. "EFSP는 사양에 문서화할 가치가 있음을 증명하는 방법으로 실습 실험과 코딩을 먼저 기반으로 한다."

### 3) Glassfish
그런 다음 JCP의 일부로 JSR에는 참조 구현이 필요했다. 이는 인터페이스를 구현하는 클래스와 약간 비슷하다 . 참조 구현은 호환되는 라이브러리의 개발자나 자체 사양 구현을 생성하려는 기타 조직에 도움이 된다.

Java EE 기능의 경우 JCP는 참조 구현을 위해 Glassfish를 사용했다.

Glassfish에 대한 이러한 중앙 집중화는 구현자의 검색 프로세스를 단순화했지만, 중앙 집중화에는 더 많은 거버넌스가 필요했으며 한 공급업체를 다른 공급업체보다 선호하는 경향이 있었다.

따라서 EFSP에는 참조 구현이 필요하지 않고 대신 호환 가능한 구현만 필요하다. 간단히 말해서, 이 미묘한 변화로 인해 Glassfish와 같은 중앙 아키텍처 내부의 구현이 재단에서 실수로 선호되지 않는다.

### 4) TCK
마지막으로 JCP에서는 기술 호환성 키트([TCK](https://projects.eclipse.org/projects/ee4j.jakartaee-tck))를 통해 EE 기능을 테스트하도록 요구했다.

TCK는 특정 EE JSR을 검증하기 위한 테스트 모음이었다. 간단히 말해서, Java EE를 준수하려면 애플리케이션 서버가 모든 JSR을 구현하고 지정된 TCK에 대한 모든 테스트를 통과해야 한다.

여기에는 큰 변화가 없다. Oracle은 TCK와 EE JSR을 오픈 소스로 제공했다. 물론 향후 모든 문서와 TCK는 오픈 소스가 될 것이다.

## [출처 및 참고]
* [https://www.baeldung.com/java-enterprise-evolution](https://www.baeldung.com/java-enterprise-evolution)
