---
title: Maven이란
author: dejavuhyo
date: 2020-11-06 14:50:00 +0900
categories: [DevOps, Maven]
tags: [maven, apache-maven, 메이븐, 아파치-메이븐]
---

## 1. 개념
Java기반 프로젝트의 라이프사이클 관리를 목적으로 하는 빌드 도구이다.

컴파일과 빌드를 동시에 수행, 테스트를 병행하거나 서버 측 Deploy 자원을 관리할 수 있는 환경을 제공한다. 또한 라이브러리 관리 기능도 내포하고 있다.

## 2. 목표
개발자가 최단 시간 내에 개발 노력의 전체 상태를 이해할 수 있도록하는 것이다. 이 목표를 달성하기 위해 Maven은 몇 가지 관심 영역을 다룬다.

### 1) 빌드 프로세스를 쉽게 만들기
Maven을 사용한다고해서 기본 메커니즘에 대해 알 필요가 없어지는 것은 아니지만 Maven은 개발자를 여러 세부 사항으로부터 보호한다.

### 2) 균일한 빌드 시스템 제공
Maven은 프로젝트 개체 모델 (POM)과 플러그인 세트를 사용하여 프로젝트를 빌드한다. 하나의 Maven 프로젝트에 익숙해지면 모든 Maven 프로젝트가 어떻게 빌드되는지 알게된다. 이렇게하면 많은 프로젝트를 탐색 할 때 시간이 절약된다.

### 3) 양질의 프로젝트 정보 제공
Maven은 부분적으로는 POM에서 가져오고 부분적으로는 프로젝트 소스에서 생성된 유용한 프로젝트 정보를 제공한다. 예를 들어 Maven은 다음을 제공 할 수 있다.

* 소스 제어에서 직접 생성 된 변경 로그
* 상호 참조 된 소스
* 프로젝트에서 관리하는 메일 링리스트
* 프로젝트에서 사용하는 종속성
* 커버리지를 포함한 단위 테스트 보고서

### 4) 더 나은 개발 관행 장려
Maven은 모범 사례 개발을위한 현재 원칙을 수집하고 프로젝트를 해당 방향으로 쉽게 안내하는 것을 목표로 한다.

예를 들어 단위 테스트의 사양, 실행 및 보고는 Maven을 사용하는 일반적인 빌드주기의 일부이다. 현재 단위 테스트 모범 사례가 지침으로 사용되었다.

* 테스트 소스 코드를 별도의 병렬 소스 트리에 보관
* 테스트 케이스 이름 지정 규칙을 사용하여 테스트를 찾고 실행
* 테스트 준비를 위해 빌드를 사용자 정의하는 대신 테스트 케이스가 환경을 설정

Maven은 릴리스 및 문제 관리와 같은 프로젝트 워크 플로우도 지원한다.

Maven은 또한 프로젝트의 디렉토리 구조를 레이아웃하는 방법에 대한 몇 가지 지침을 제안한다. 레이아웃을 배우면 Maven을 사용하는 다른 프로젝트를 쉽게 탐색 할 수 있다.

프로젝트 레이아웃에 대해 독단적인 접근 방식을 취하지만 일부 프로젝트는 역사적 이유로 이 구조에 맞지 않을 수 있다. Maven은 다양한 프로젝트의 요구에 유연하게 맞출 수 있도록 설계되었지만 목표를 손상시키지 않고 모든 상황을 충족시킬 수는 없다.

프로젝트에 재구성 할 수 없는 비정상적인 빌드 구조가 있는 경우 일부 기능을 포기하거나 Maven을 아예 사용하지 않아야 할 수 있습니다.

![img001](/assets/img/2020-11-06-what-is-maven/img001.png)

## 3. 차이점

### 1) Ant와 차이점

* Ant가 비교적 자유도가 높다. 전처리, 컴파일, 패키징, 테스팅, 배포 가능
* Maven은 정해진 라이프사이클에 의하여 작업을 수행하며, 전반적인 프로젝트 관리 기능까지 포함하고 있음 (Build Tool + Project Management)

### 2) Gradle과 차이점

* XML 대신 groovy 스크립트를 사용하여 동적인 빌드 가능.
* Maven은 멀티프로젝트에서 상속구조인데 Gradle은 주입 방식이다. 멀티프로젝트에서 Gradle이 더 적합하다.

## 4. Maven Lifecycle

### 1) Lifecycle
메이븐은 프레임워크이기 때문에 동작 방식이 정해져있고, 미리 정의하고 있는 빌드 순서가 있다. 이를 라이프사이클(Lifecycle)이라 한다.

* **Default(Build):** 일반적인 빌드 프로세스를 위한 모델
* **Clean:** 빌드시 생성되었던 파일들을 삭제하는 단계
* **Validate:** 프로젝트가 올바른지 확인하고 필요한 모든 정보를 사용할 수 있는지 확인하는 단계
* **Compile:** 프로젝트의 소스코드를 컴파일 하는 단계
* **Test:** 유닛(단위) 테스트를 수행 하는 단계(테스트 실패시 빌드 실패로 처리, 스킵 가능)
* **Package:** 실제 컴파일된 소스 코드와 리소스들을 jar, war 등의 배포를 위한 패키지로 만드는 단계
* **Verify:** 통합 테스트 결과에 대한 검사를 실행하여 품질 기준을 충족하는지 확인하는 단계
* **Install:** 패키지를 로컬 저장소에 설치하는 단계
* **Site:** 프로젝트 문서와 사이트 작성, 생성하는 단계
* **Deploy:** 만들어진 package를 원격 저장소에 release하는 단계

최종 빌드 순서는 compile → test → package 이다.

* **compile:** src/main/java 디렉토리 아래의 모든 소스 코드가 컴파일 된다.
* **test:** src/test/java, src/test/resources 테스트 자원 복사 및 테스트 소스 코드 컴파일 된다.
* **packaging:** 컴파일과 테스트가 완료 된 후, jar, war 같은 형태로 압축하는 작업.

### 2) Phase
Build Lifecycle의 각각의 단계를 Phase라고 한다. Phase는 의존관계를 가지고 있어 해당 Phase가 수행되려면 이전 단계의 Phase가 모두 수행되어야 한다. 즉, 모든 빌드단계는 이전 단계가 성공적으로 실행되었을 때 실행된다는 것이 Dependency 이다.

### 3) Goal
특정 작업, 최소한의 실행 단위(task)로 하나의 플러그인에서는 여러 작업을 수행할 수 있도록 지원하며, 플러그인에서 실행할 수 있는 각각의 기능(명령)을 Goal이라고 한다. (각각의 Phase에 연계된 Goal을 실행하는 과정을 Build라고 한다.)

## [출처 및 참고]
* [https://maven.apache.org/what-is-maven.html](https://maven.apache.org/what-is-maven.html)
* [https://sjh836.tistory.com/131](https://sjh836.tistory.com/131)
* [https://goddaehee.tistory.com/199](https://goddaehee.tistory.com/199)
