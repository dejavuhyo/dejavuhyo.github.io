---
title: Maven 플러그인 버전
author: dejavuhyo
date: 2025-05-09 09:25:00 +0900
categories: [DevOps, Maven]
tags: [maven, maven-version, 메이븐, 메이븐-버전, 버전-표기]
---

## 1. Maven 버전 표기 이유
Maven에서 버전 표기를 사용하는 이유는 개발 과정에서 특정 기능의 완성도를 나타내고, 사용자에게 현재 개발 상태를 알려주기 위함이다.

개발자들이 M1 버전부터 해당 버전을 사용하며 피드백을 제공하고, 이 정보를 바탕으로 릴리즈 버전으로 발전시켜 나갈 수 있다. 이러한 버전 번호 표현 방식은 Maven 프로젝트를 개발하고 관리하는 데 중요한 역할을 한다.

Maven 프로젝트에서 사용하는 플러그인이나 라이브러리의 버전 정보를 정확하게 파악하여 프로젝트의 안정성을 높이고, 보안 취약점을 방지하는데 도움이 된다.

## 2. Maven 버전 종류

### 1) SNAPSHOT
Maven의 SNAPSHOT 버전은 아직 최종적으로 릴리스되지 않은 개발 중인 버전을 의미한다. 즉, `1.0-SNAPSHOT`은 1.0 버전을 기반으로 개발 중인 버전임을 나타낸다.

이 버전은 개발 과정에서 변경될 수 있으며, 안정적인 릴리스 버전과 구별된다.

### 2) M1, M2 등 (Milestone)
Maven 버전에서 "M1"은 Milestone 1을 의미하며, 이는 프로젝트가 주요 기능이 거의 완성되었지만 아직 정식 릴리즈 버전이 아닌 테스트용 버전임을 나타낸다.

이 버전은 주로 개발자 내부에서 테스트 및 피드백을 위해 사용된다.

### 3) RC (Release Candidate)
Maven에서 "RC"는 "Release Candidate"를 의미하며, 최종 정식 릴리스 버전이 되기 전에 개발팀이 최종적으로 테스트하고 버그를 수정하여 출시 직전의 후보 버전이다.

### 4) GA (Generally Available)
Maven에서 GA (General Availability)는 일반 사용자들이 사용할 수 있는 안정적인 출시 버전을 의미한다.

Maven 중앙 저장소에 배포되는 가장 최신이고 안정적인 버전으로, 새로운 프로젝트에 주로 사용된다.

### 5) RELEASE
Maven에서 RELEASE 버전은 안정적이고 완성된 소프트웨어 버전을 의미한다.

개발 중인 스냅샷 버전(SNAPSHOT)과 달리, 릴리스 버전은 모든 개발 과정을 거쳐 테스트를 완료하고 배포가 가능한 상태이다.

릴리스 버전은 일반적인 버전 지정자(예: 1.0, 2.5.1)로 표시되고, 변경이 발생하면 새로운 릴리스 버전이 만들어진다.

### 6) SEC
SEC는 "Security" (보안)을 의미하는 약자로, Maven 버전 번호에서 "SEC" 또는 "SEC01"은 일반적으로 특정 기능 또는 변경 사항의 범위를 나타내는 검증된 버그 수정 또는 보안 업데이트를 의미한다.

이 표현은 Maven 플러그인이나 Maven을 사용하는 프로젝트에서 사용되는 버그 수정이나 보안 패치 업데이트를 나타낼 수 있다.

## 3. Maven 버전 구성
Maven은 자바 프로젝트 빌드 및 의존성 관리에 사용되는 도구이다. 각 버전은 주요 버전, 부 버전, 패치 버전으로 구성된다.

예를 들어, `2.5.6`은 주요 버전 `2`, 부 버전 `5`, 패치 버전 `6`을 의미한다.

## [출처 및 참고]
* [https://sallykim5087.tistory.com/207](https://sallykim5087.tistory.com/207)
* [https://www.mojohaus.org/versions/versions-maven-plugin/index.html](https://www.mojohaus.org/versions/versions-maven-plugin/index.html)
