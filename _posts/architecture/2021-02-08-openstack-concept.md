---
title: OpenStack 개념
author: dejavuhyo
date: 2021-02-08 19:50:00 +0900
categories: [Language, Architecture]
tags: [openstack-concept, openstack, openstack-개념]
---

## 1. OpenStack 이란
OpenStack은 표준 하드웨어에서 운용할 수 있는 모듈형 클라우드 인프라를 제공하여 단일한 위치에서 필요한 모든 툴을 필요한 시기에 배포할 수 있다.

가상화에서는 다양한 벤더별 프로그램에서 스토리지, CPU, RAM과 같은 리소스를 추상화해 하이퍼바이저를 통해 분할한 다음 필요에 따라 배포합니다. OpenStack은 일관된 애플리케이션 프로그래밍 인터페이스(Application Programming Interface, API) 세트를 사용해 해당 가상 리소스를 분리된 풀로 1단계 더 추상화하며, 이를 기반으로 하는 표준 클라우드 컴퓨팅 툴에서 관리자와 사용자가 직접 인터랙션할 수 있다.

[![OpenStack 이란](https://i.ytimg.com/vi_webp/9-br7qDuuK4/sddefault.webp)](https://www.youtube.com/watch?v=9-br7qDuuK4&feature=emb_logo)

## 2. 단순한 가상화 관리 플랫폼인가
OpenStack과 가상화 관리 플랫폼은 모두 가상 리소스를 기반으로 하며 벤더별 환경에서 프로세스를 검색, 리포트, 자동화할 수 있다.

그러나 가상화 관리 플랫폼에서는 가상 리소스의 기능을 조작하는 것이 간편한 반면, OpenStack에서는 툴 조합을 실행하기 위해 실제로 가상 리소스를 사용한다는 차이가 있다. 이러한 툴은 미국 국립표준기술원(NIST)이 제시한 클라우드 컴퓨팅의 5가지 기준인 네트워크, 풀링된 리소스, 사용자 인터페이스, 프로비저닝 기능, 자동 리소스 제어/할당을 충족하는 클라우드 환경을 구축한다.

## 3. 작동 방법
OpenStack은 기본적으로 스크립트라고하는 일련의 명령이다. 이러한 스크립트는 프로젝트라 불리는 패키지로 구성되어 클라우드 환경을 구축하는 태스크를 전달한다. OpenStack은 이러한 환경을 구축하기 위해 두 가지 유형의 소프트웨어를 사용한다.

* 하드웨어에서 추상화된 가상 리소스 레이어를 생성하는 가상화

* OpenStack 스크립트에서 제공되는 명령을 실행하는 기본 운영 체제(OS)

OpenStack은 자체적으로 리소스를 가상화하는 것이 아니라, 리소스를 사용하여 클라우드를 구축g한다. OpenStack은 또한 명령을 실행하기보다는 기본 OS로 이들 명령을 전달한다. 따라서 OpenStack, 가상화 및 기본 OS의 세 가지 기술이 모두 연동되어야 한다. 그러한 상호의존성 때문에 OpenStack 클라우드는 Linux를 사용하여 배포된다. 이는 OpenStack을 오픈소스 소프트웨어로 출시하기로 결정한 RackSpace와 NASA의 사례에도 영향을 미쳤다.

[![작동 방법](https://i.ytimg.com/vi_webp/Ir05ZGX4IT4/sddefault.webp)](https://www.youtube.com/watch?v=Ir05ZGX4IT4&feature=emb_logo)

## 4. 구성 요소
OpenStack의 아키텍처는 수많은 오픈소스 프로젝트로 이루어져 있다. 이 프로젝트는 OpenStack의 언더클라우드 및 오버클라우드(각각 시스템 관리자 및 클라우드 사용자가 이용)를 설정하는 데 사용된다. 언더클라우드는 시스템 관리자가 최종 사용자의 OpenStack 환경, 즉 오버클라우드를 설정하고 관리하는 데 필요한 코어 구성 요소로 이루어진다.

컴퓨팅, 네트워킹, 스토리지, ID, 이미지를 처리하는 6가지의 안정적인 핵심 서비스가 있으며, 개발이 진행됨에 따라 수십 가지 이상의 옵션이 제공된다. 이 6가지 핵심 서비스는 나머지 프로젝트에서 대시보드, 오케스트레이션, 베어메탈(bare metal) 프로비저닝, 메시징, 컨테이너, 거버넌스를 처리할 수 있도록 하는 인프라가 된다.

### 1) NOVA
[Nova](https://www.openstack.org/software/releases/ocata/components/nova)는 OpenStack 컴퓨팅 리소스를 위한 전체 관리 및 액세스 툴로 스케줄링, 생성, 삭제를 처리한다.

### 2) NEUTRON
[Neutron](https://www.openstack.org/software/releases/ocata/components/neutron)은 기타 OpenStack 서비스 전반에서 네트워크를 연결한다.

### 3) SWIFT
[Swift](https://www.openstack.org/software/releases/ocata/components/swift)는 내결함성이 뛰어난 오브젝트 스토리지 서비스로, RESTful API를 사용해 구조화되지 않은 애플리케이션을 저장 및 검색한다.

### 4) CINDER
[Cinder](https://www.openstack.org/software/releases/ocata/components/cinder)는 셀프 서비스 API를 통해 액세스할 수 있는 퍼시스턴트 블록 스토리지이다.

### 5) KEYSTONE
[Keystone](https://www.openstack.org/software/releases/ocata/components/keystone)은 모든 OpenStack 서비스를 인증하고 권한을 부여하며 모든 서비스를 위한 엔드포인트 카탈로그의 역할도 한다.

### 6) GLANCE
[Glance](https://www.openstack.org/software/releases/ocata/components/glance)는 다양한 위치에 있는 가상 머신 디스크의 이미지를 저장하고 검색한다.

## 5. 지원 요소

### 1) 프라이빗 클라우드
OpenStack에서 실행되는 프라이빗 클라우드 배포는 DIY 접근 방식보다 효과적이라는 것이 밝혀졌다. [451 Research](https://www.redhat.com/ko/resources/openstack-use-case-private-cloud)의 연구 결과에 따르면 OpenStack은 설치와 관리가 간편하기 때문에 OpenStack 배포 시 가상 머신 수를 6%만큼만 더 증가시켜도 사용자가 직접 생성한 프라이빗 클라우드보다 큰 가치를 제공하는 것으로 나타났다.

### 2) 네트워크 기능 가상화
451 Research의 연구에 따르면 여러 환경에 배포할 수 있도록 네트워크의 주요 기능을 분리해야 하는 네트워크 기능 가상화(Network Functions Virtualization, NFV)에서 OpenStack을 사용하는 것은 매우 효과적인 차세대 접근 방식임이 밝혀졌다. 이는 애널리스트가 설문조사를 실시한 거의 모든 글로벌 통신 서비스 제공업체에서 중요하게 다루는 주제이기도 하다.

### 3) 퍼블릭 클라우드
OpenStack은 퍼블릭 클라우드 환경 구축을 위한 선도적인 오픈소스 솔루션이다. 수십억 달러 규모의 기업이든 스타트업 기업이든 OpenStack을 사용해 주요 퍼블릭 클라우드 제공업체와 어깨를 나란히 하는 서비스로 퍼블릭 클라우드를 구축할 수 있다.

### 4) 컨테이너
OpenStack은 프라이빗 클라우드와 퍼블릭 클라우드 모두를 위한 안정적인 기반을 제공한다. 컨테이너는 애플케이션 제공 속도를 높이면서 애플리케이션의 배포와 관리를 간소화한다. OpenStack에서 컨테이너를 실행하면 사일로화된 단일 팀에서 전사적인 부서 간 운영에 이르기까지 컨테이너의 이점을 얻을 수 있다.

## [출처 및 참고]
* <https://www.redhat.com/ko/topics/openstack>
