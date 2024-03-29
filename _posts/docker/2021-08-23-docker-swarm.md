---
title: Docker Swarm
author: dejavuhyo
date: 2021-08-23 06:00:00 +0900
categories: [DevOps, Docker]
tags: [docker-swarm, docker-swarm-mode, swarm, 도커-스웜, 도커-스웜-모드, 스웜]
---

## 1. Docker Swarm이란

![docker-swarm](/assets/img/2021-08-23-docker-swarm/docker-swarm.png)

Docker Swarm은 Docker 애플리케이션을 실행하고 클러스터에서 함께 결합하도록 구성된 물리적 또는 가상 머신의 그룹이다. 머신 그룹이 함께 클러스터링 되면 익숙한 Docker 명령을 계속 실행할 수 있지만 이제 클러스터의 머신에서 수행된다. 클러스터의 활동은 Swarm Manager에 의해 제어되며 클러스터에 가입한 시스템을 노드라고 한다.

## 2. 사용 용도
Docker Swarm은 컨테이너 오케스트레이션 도구이다. 즉, 사용자가 여러 호스트 시스템에 배포된 여러 컨테이너를 관리할 수 있다.

Docker Swarm 운영과 관련된 주요 이점 중 하나는 애플리케이션에 제공되는 높은 수준의 가용성이다. 도커 스웜에는 일반적으로 작업자 노드의 리소스를 효율적으로 처리하고 클러스터가 효율적으로 작동하도록 하는 책임이 있는 여러 작업자 노드와 하나 이상의 관리자 노드가 있다.

## 3. 서비스 유형
Docker Swarm에는 replicated 및 global 두 가지 서비스 유형이 있다.

* **Replicated services:** 스웜 모드 복제 서비스는 스웜 관리자가 사용 가능한 노드에 할당할 복제 작업의 수를 지정하여 작동한다.

* **Global services:** 글로벌 서비스는 스웜 관리자를 사용하여 서비스 제약 및 리소스 요구 사항을 충족하는 사용 가능한 각 노드에 대해 하나의 작업을 예약함으로써 기능하다.

## 4. 노드
도커 스웜은 클러스터에서 작동하는 물리적 또는 가상 머신 그룹으로 구성된다. 머신이 클러스터에 합류하면 해당 스웜의 노드가 된다. Docker Swarm 기능은 docker swarm 생태계 내에서 각각 다른 역할을 하는 세 가지 유형의 노드를 인식한다.

### 1) Docker Swarm Manager Node
관리자 노드의 주요 기능은 스웜의 작업자 노드에 작업을 할당하는 것이다. 관리자 노드는 또한 무리를 운영하는데 필요한 일부 관리 작업을 수행하는데 도움이 된다. Docker는 스웜에 대해 최대 7개의 관리자 노드를 권장한다.

### 2) Docker Swarm Leader Node
클러스터가 설정되면 Raft 합의 알고리즘을 사용하여 그중 하나를 '리더 노드'로 할당한다. 리더 노드는 스웜에 대한 모든 스웜 관리 및 작업 오케스트레이션 결정을 내린다. 정전 또는 장애로 인해 리더 노드를 사용할 수 없게 되면 Raft 합의 알고리즘을 사용하여 새로운 리더 노드를 선출할 수 있다.

### 3) Docker Swarm Worker Node
수많은 호스트가 있는 도커 스웜에서 각 작업자 노드는 관리자 노드에서 할당한 작업을 수신하고 실행하여 기능하다. 기본적으로 모든 관리자 모드는 작업자 노드이기도 하며 사용 가능한 리소스가 있을 때 작업을 실행할 수 있다.

## 5. 주요 기능

### 1) 도커 엔진과 통합된 클러스터 관리
Docker Engine CLI를 사용하여 애플리케이션 서비스를 배포할 수 있는 Docker Engine의 무리를 만든다. 무리를 생성하거나 관리하는데 추가 조정 소프트웨어가 필요하지 않다.

### 2) 분산 설계
배포 시 노드 역할 간의 차이를 처리하는 대신 Docker Engine은 런타임에 전문화를 처리한다. Docker 엔진을 사용하여 노드, 관리자 및 작업자를 모두 배포할 수 있다. 즉, 단일 디스크 이미지로 전체 스와트를 작성할 수 있다.

### 3) 선언적 서비스 모델
Docker Engine은 선언적 접근 방식을 사용하여 응용프로그램 스택에서 원하는 다양한 서비스의 상태를 정의할 수 있다. 예를 들어 메시지 대기열 서비스와 데이터베이스 백엔드가 있는 웹 프런트 엔드 서비스로 구성된 응용 프로그램을 설명할 수 있다.

### 4) 스케일링
각 서비스에 대해 실행할 태스크 수를 선언할 수 있다. 스케일업 또는 스케일다운할 때 원하는 상태를 유지하기 위한 작업을 추가하거나 제거하여 스와트 관리자가 자동으로 적응한다.

### 5) 원하는 상태 조정
swarm manager 노드는 클러스터 상태를 지속적으로 모니터링하고 실제 상태와 표현된 원하는 상태 간의 차이를 조정한다. 예를 들어, 컨테이너의 복제본을 10개 실행하도록 서비스를 설정한 경우, 해당 복제본 중 2개를 호스트하는 작업자 컴퓨터가 충돌하면 관리자는 충돌한 복제본을 대체하기 위해 두 개의 새 복제본을 작성한다. swarm 관리자는 실행 중이며 사용 가능한 작업자에게 새 복제본을 할당한다.

### 6) 다중 호스트 네트워킹
서비스에 대한 오버레이 네트워크를 지정할 수 있다. swarm manager는 응용 프로그램을 초기화하거나 업데이트할 때 오버레이 네트워크의 컨테이너에 주소를 자동으로 할당한다.

### 7) 서비스 검색
Swarm 관리자 노드는 실행 중인 컨테이너의 각 서비스에 고유한 DNS 이름과 로드 밸런싱을 할당한다. 군집 내에 포함된 DNS 서버를 통해 군집 내에서 실행 중인 모든 컨테이너를 쿼리할 수 있다.

### 8) 로드 밸런싱
서비스용 포트를 외부 로드 밸런서에 노출할 수 있다. 내부적으로 서비스 컨테이너를 노드 간에 배포하는 방법을 지정할 수 있다.

### 9) 기본적으로 보안
군집의 각 노드는 TLS 상호 인증 및 암호화를 적용하여 자신과 다른 모든 노드 간의 통신을 보호한다. 자체 서명된 루트 인증서 또는 사용자 정의 루트 CA의 인증서를 사용할 수 있다.

### 10) 롤링 업데이트
원격 설치 시 서비스 업데이트를 노드에 증분 적용할 수 있다. swarm manager를 사용하면 서로 다른 노드 집합에 대한 서비스 배포 간의 지연을 제어할 수 있다. 문제가 발생하면 이전 버전의 서비스로 롤백할 수 있다.

## [출처 및 참고]
* [https://docs.docker.com/engine/swarm/](https://docs.docker.com/engine/swarm/)
* [https://www.sumologickorea.com/glossary/docker-swarm/](https://www.sumologickorea.com/glossary/docker-swarm/)
* [https://subicura.com/2017/02/25/container-orchestration-with-docker-swarm.html](https://subicura.com/2017/02/25/container-orchestration-with-docker-swarm.html)
