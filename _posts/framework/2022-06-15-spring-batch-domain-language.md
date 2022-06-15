---
title: 배치의 도메인 언어
author: dejavuhyo
date: 2022-06-15 17:20:00 +0900
categories: [Application, Framework]
tags: [batch-domain-language, spring-batch, spring-batch-domain, 배치-도메인-언어, 배치-도메인, 스프링-배치]
---

## 1. 배치의 도메인 언어
숙련된 배치 설계자에게 Spring Batch에서 사용되는 일괄 처리의 전반적인 개념은 익숙하고 편안해야 한다. "Jobs" 및 "Steps" 그리고 `ItemReader`와 `ItemWriter`라는 개발자 제공 처리 장치 유닛이 있다. 그러나 Spring 패턴, 작업, 템플릿, 콜백 및 관용구로 인해 다음과 같은 기회가 있다.

* 명확한 관심사 분리를 준수하는데 상당한 개선이 있다.

* 인터페이스로 제공되는 명확하게 설명된 아키텍처 계층 및 서비스.

* 빠른 채택과 즉시 사용의 용이성을 허용하는 단순하고 기본 구현이다.

* 확장성이 크게 향상되었다.

다음 다이어그램은 수십 년 동안 사용되어 온 배치 참조 아키텍처의 단순화된 버전이다. 일괄 처리의 도메인 언어를 구성하는 구성 요소에 대한 개요를 제공한다.

이 아키텍처 프레임워크는 지난 몇 세대의 플랫폼(`COBOL/Mainframe`, `C/Unix` 및 현재 `Java/anywhere`)에서 수십 년간 구현을 통해 입증된 청사진이다. `JCL` 및 `COBOL` 개발자는 `C`, `C#` 및 `Java` 개발자만큼 개념에 익숙할 것이다.

Spring Batch는 매우 복잡한 처리 요구 사항을 해결하기 위한 인프라 및 확장과 함께 단순에서 복잡한 일괄 애플리케이션의 생성을 처리하는 데 사용되는 강력하고 유지 관리 가능한 시스템에서 일반적으로 발견되는 계층, 구성 요소 및 기술 서비스의 물리적 구현을 제공한다.

* Batch Stereotypes

![spring-batch-reference-model](/assets/img/2022-06-15-spring-batch-domain-language/spring-batch-reference-model.png)

위의 다이어그램은 Spring Batch의 도메인 언어를 구성하는 주요 개념을 강조한다. 작업에는 일대다 단계가 있으며, 각 단계는 정확히 하나의 `ItemReader`, 하나의 `ItemProcessor` 및 하나의 `ItemWriter`가 있다. 작업은 `JobLauncher를` 사용하여 시작해야 하며, 현재 실행 중인 프로세스에 대한 메타데이터를 `JobRepository`에 저장해야 한다.

## [출처 및 참고]
* <https://docs.spring.io/spring-batch/docs/4.2.x/reference/html/index-single.html#domainLanguageOfBatch>
